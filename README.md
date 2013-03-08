Phing Drupal Module Template
============================

This is a Phing buildfile with targets for performing static analysis of Drupal modules.

Based on the [Phing Drupal Template](https://github.com/reload/phing-drupal-template) by the good folks at Reload!

Requirements
------------

This buildfile requries that some tools are available in specific locations under the `/opt` directory. You can use the following snippets in a Puppet manifest to make sure they're available:

### Download and install jslint tools

    exec { 'download-jslint4java':
      command => 'wget -P /root https://jslint4java.googlecode.com/files/jslint4java-2.0.2-dist.zip',
      creates => '/root/jslint4java-2.0.2-dist.zip',
    }

    exec { 'install-jslint4java':
      command => 'unzip -q jslint4java-2.0.2-dist.zip && mv jslint4java-2.0.2 /opt && chmod 755 /opt/jslint4java-2.0.2',
      cwd     => '/root',
      creates => '/opt/jslint4java-2.0.2',
      require => Exec['download-jslint4java'],
    }

    file { '/opt/jslint':
      ensure => directory,
    }

    exec { 'download-fulljslint':
      command => 'wget https://raw.github.com/mikewest/JSLint/master/fulljslint.js',
      cwd     => '/opt/jslint',
      creates => '/opt/jslint/fulljslint.js',
      require => File['/opt/jslint'],
    }

### Download and install rhino

    exec { 'download-rhino':
      command => 'wget -P /root http://ftp.mozilla.org/pub/mozilla.org/js/rhino1_7R3.zip',
      creates => '/root/rhino1_7R3.zip',
    }

    exec { 'install-rhino':
      command => 'unzip -q rhino1_7R3.zip && mv rhino1_7R3 /opt',
      cwd     => '/root',
      creates => '/opt/rhino1_7R3',
      require => Exec['download-rhino'],
    }

    file { '/opt/csslint':
      ensure => directory,
    }

    exec { 'download-csslint':
      command => 'wget https://raw.github.com/stubbornella/csslint/master/release/csslint-rhino.js',
      cwd     => '/opt/csslint',
      creates => '/opt/csslint/csslint-rhino.js',
      require => File['/opt/csslint'],
    }

### Download and install phing phploc integration

    file { '/opt/phploctask':
      ensure => directory,
    }

    exec { 'download-phploctask':
      command => 'wget https://raw.github.com/raphaelstolt/phploc-phing/master/PHPLocTask.php',
      cwd     => '/opt/phploctask',
      creates => '/opt/phploctask/PHPLocTask.php',
      require => File['/opt/phploctask'],
    }

### Download Drupal codesniffer rules

    exec { 'install-drupal-coder':
      command => 'git clone --branch 7.x-2.x http://git.drupal.org/project/coder.git /opt/coder',
      creates => '/opt/coder/coder_sniffer/Drupal/ruleset.xml',
    }

    file { '/usr/share/php/PHP/CodeSniffer/Standards/Drupal':
      ensure => link,
      target => '/opt/coder/coder_sniffer/Drupal',
      require => Exec['install-drupal-coder'],
    }
