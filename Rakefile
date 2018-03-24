require 'erb'

namespace :rpm do
  namespace :ngx_mruby do
    desc 'Build nginx-module-mruby'
    task all: ['rpm:ngx_mruby:prep',
               'rpm:ngx_mruby:generate_spec',
               'rpm:ngx_mruby:build',
               'rpm:ngx_mruby:release',
               'rpm:ngx_mruby:check']

    task :prep do
      `rpmdev-setuptree`
      ENV['OS_MAJOR_VERSION'] = `cat /etc/redhat-release`.chomp.gsub(/^[^\d]+(\d).+/, '\1')
      ENV['NGINX_VERSION'] = `yum info nginx | grep Version | awk '{print $3}'`.chomp
      ENV['NGINX_RELEASE'] = `yum info nginx | grep Release | awk '{print $3}'`.chomp
      ENV['NGINX_ARCH'] = `yum info nginx | grep Arch | awk '{print $3}'`.chomp
      ENV['NGX_MRUBY_VERSION'] = `curl -s https://api.github.com/repos/matsumotory/ngx_mruby/releases | jq -r '.[0].tag_name' | sed 's/v//'`.chomp
      ENV['NDK_VERSION'] = `curl -s https://api.github.com/repos/simplresty/ngx_devel_kit/tags | jq -r '.[].name' | grep -v rc | sed 's/v//' | head -n 1`.chomp
    end

    task :generate_spec do
      spec = ERB.new(File.read('nginx-module-mruby.spec.erb')).result(binding)
      File.write('nginx-module-mruby.spec', spec)
    end

    task :build do
      `rpmdev-setuptree`
      `spectool -g -R nginx-module-mruby.spec`
      `rpmbuild -ba nginx-module-mruby.spec`
    end

    task :check do
      rpm = "nginx-module-mruby-#{ENV['NGX_MRUBY_VERSION']}.ngx#{ENV['NGINX_VERSION']}-#{ENV['NGINX_RELEASE'].gsub(/\.ngx/, '')}.#{ENV['NGINX_ARCH']}.rpm"
      rpmfile = "/root/rpmbuild/RPMS/#{ENV['NGINX_ARCH']}/#{rpm}"
      raise "#{rpmfile} not exist" unless File.exist?(rpmfile)
      puts `yum install -y nginx`
      puts `yum install -y #{rpmfile}`
    end

    desc 'Release nginx-module-mruby'
    task :release do
      if ENV['CIRCLE_BRANCH'] == 'master'
        rpm = "nginx-module-mruby-#{ENV['NGX_MRUBY_VERSION']}.ngx#{ENV['NGINX_VERSION']}-#{ENV['NGINX_RELEASE'].gsub(/\.ngx/, '')}.#{ENV['NGINX_ARCH']}.rpm"
        rpmfile = "/root/rpmbuild/RPMS/#{ENV['NGINX_ARCH']}/#{rpm}"
        status_code = `curl -LI https://packagecloud.io/k1low/nginx-module-mruby/packages/el/#{ENV['OS_MAJOR_VERSION']}/#{rpm} -o /dev/null -w '%{http_code}\n' -s`.chomp
        if status_code == '200'
          puts "#{rpm} already exists."
        else
          cmd = "package_cloud push k1low/nginx-module-mruby/el/#{ENV['OS_MAJOR_VERSION']} #{rpmfile}"
          puts cmd
          system(cmd)
        end
      end
    end
  end

  namespace :ndk do
    task all: ['rpm:ndk:prep',
               'rpm:ndk:generate_spec',
               'rpm:ndk:build',
               'rpm:ndk:release',
               'rpm:ndk:check']

    desc 'Build nginx-module-ndk'
    task :prep do
      `rpmdev-setuptree`
      ENV['OS_MAJOR_VERSION'] = `cat /etc/redhat-release`.chomp.gsub(/^[^\d]+(\d).+/, '\1')
      ENV['NGINX_VERSION'] = `yum info nginx | grep Version | awk '{print $3}'`.chomp
      ENV['NGINX_RELEASE'] = `yum info nginx | grep Release | awk '{print $3}'`.chomp
      ENV['NGINX_ARCH'] = `yum info nginx | grep Arch | awk '{print $3}'`.chomp
      ENV['NGX_MRUBY_VERSION'] = `curl -s https://api.github.com/repos/matsumotory/ngx_mruby/releases | jq -r '.[0].tag_name' | sed 's/v//'`.chomp
      ENV['NDK_VERSION'] = `curl -s https://api.github.com/repos/simplresty/ngx_devel_kit/tags | jq -r '.[].name' | grep -v rc | sed 's/v//' | head -n 1`.chomp
    end

    task :generate_spec do
      spec = ERB.new(File.read('nginx-module-ndk.spec.erb')).result(binding)
      File.write('nginx-module-ndk.spec', spec)
    end

    task :build do
      `rpmdev-setuptree`
      `spectool -g -R nginx-module-ndk.spec`
      `rpmbuild -ba nginx-module-ndk.spec`
    end

    task :check do
      rpmfile = "/root/rpmbuild/RPMS/#{ENV['NGINX_ARCH']}/nginx-module-ndk-#{ENV['NDK_VERSION']}.ngx#{ENV['NGINX_VERSION']}-#{ENV['NGINX_RELEASE'].gsub(/\.ngx/, '')}.#{ENV['NGINX_ARCH']}.rpm"
      raise "#{rpmfile} not exist" unless File.exist?(rpmfile)
      puts `yum install -y nginx`
      puts `yum install -y #{rpmfile}`
    end

    desc 'Release nginx-module-ndk'
    task :release do
      if ENV['CIRCLE_BRANCH'] == 'master'
        rpm = "nginx-module-ndk-#{ENV['NDK_VERSION']}.ngx#{ENV['NGINX_VERSION']}-#{ENV['NGINX_RELEASE'].gsub(/\.ngx/, '')}.#{ENV['NGINX_ARCH']}.rpm"
        rpmfile = "/root/rpmbuild/RPMS/#{ENV['NGINX_ARCH']}/#{rpm}"
        status_code = `curl -LI https://packagecloud.io/k1low/nginx-module-mruby/packages/el/#{ENV['OS_MAJOR_VERSION']}/#{rpm} -o /dev/null -w '%{http_code}\n' -s`.chomp
        if status_code == '200'
          puts "#{rpm} already exists."
        else
          cmd = "package_cloud push k1low/nginx-module-mruby/el/#{ENV['OS_MAJOR_VERSION']} #{rpmfile}"
          puts cmd
          system(cmd)
        end
      end
    end
  end
end
