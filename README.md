Phing Drupal Module Template
============================

This is a Phing buildfile with targets for performing static analysis of Drupal modules.

Based on the [Phing Drupal Template](https://github.com/reload/phing-drupal-template) by the good folks at Reload!

Requirements
------------

This buildfile requries that JShint and CSSlint are available. You can use the following snippets in a Puppet manifest to make sure they're available:

### Download and install JShint

    exec { 'npm-install-jshint':
      command => 'npm install -g jshint',
      creates => '/usr/local/bin/jshint',
      require => Package['npm'],
    }

    exec { 'npm-update-jshint':
      command => 'npm update -g jshint',
      require => Exec['npm-install-jshint'],
    }

### Download and install CSSlint

    exec { 'npm-install-csslint':
      command => 'npm install -g csslint',
      creates => '/usr/local/bin/csslint',
      require => Package['npm'],
    }

    exec { 'npm-update-csslint':
      command => 'npm update -g csslint',
      require => Exec['npm-install-csslint'],
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
      require => [Exec['install-drupal-coder'], Class['php::qatools']],
    }
