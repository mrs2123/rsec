# rsec
require 'chefspec'

describe 'sonar::default' do
  let(:chef_run) { ChefSpec::ServerRunner.new(platform: 'centos', version: '7.1.1503').converge(described_recipe) }

#require 'spec_helper'
sonarqube_zipfile_destination = ::File.join(Chef::Config[:file_cache_path], "sonarqube-5.6.6.zip")
#describe 'sonar::default' do
#  context 'When all attributes are default, on an unspecified platform' do
#    let(:chef_run) do
#      runner = ChefSpec::ServerRunner.new
#     runner.converge(described_recipe)
#    end

    it 'creates a group ' do
    expect(chef_run).to create_group('sonargrp')
   end
   
   
  it 'creates a user ' do	
    expect(chef_run).to create_user('sonaruser').with(group: 'sonargrp')
	end
    
  it 'installs a yum_package java-1.8.0-openjdk' do
    expect(chef_run).to install_yum_package('java-1.8.0-openjdk').with(options: '--nogpgcheck')
    end
	
  it 'creates a remote_file ' do
   #expect(chef_run).to create_remote_file(::File.join(Chef::Config[:file_cache_path],"sonarqube-5.6.6.zip")).with(mode: '0644')
   expect(chef_run).to create_remote_file(::File.join(Chef::Config[:file_cache_path], "sonarqube-5.6.6.zip"))
   #expect(chef_run).to not_create_remote_file('sonarqube_5.6.7.zip')
   #expect(chef_run).to create_remote_file('sonarqube_5.6.6.zip').with(mode: '644')
	end
	
  it 'installs unzip package' do
    expect(chef_run).to install_package('unzip')
    end
	
  it 'runs a bash script ' do
    expect(chef_run).to run_bash('unzip installation')
	#.with(code: '<<-EOF
    #unzip  #{sonarqube_zipfile_destination} -d /opt/;
    #chown -R sonaruser:sonargrp /opt/sonarqube-5.6.6
  #EOF')
   end
   
   
  it 'creates a directory ' do
    expect(chef_run).to create_directory('/opt/sonarqube-5.6.6/conf').with(
      user:   'sonaruser',
      group:  'sonargrp'
    )
  end
  
   it 'creates a softlink' do
    expect(chef_run).to create_link('/usr/bin/sonarqube').with(to: '/opt/sonarqube-5.6.6/bin/linux-x86-64/sonar.sh')    
  end
  
   it 'creates a template' do
    expect(chef_run).to create_template('/etc/init.d/sonarqube').with(
      source:   'sonarqube.erb',
	  
	  user: 'root',
      group:  'root'
    )
  end

  
end
