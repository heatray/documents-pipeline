defaults = [
  branch:          'experimental',
  version:         '99.99.99',
  clean:           true,
  windows_x64:     true,
  windows_x86:     true,
  windows_x64_xp:  true,
  windows_x86_xp:  true,
  macos_x86_64:    true,
  macos_x86_64_v8: true,
  macos_arm64:     true,
  linux_x86_64:    true,
  linux_aarch64:   true,
  android:         true,
  core:            true,
  desktop:         true,
  builder:         true,
  server_ce:       true,
  server_ee:       true,
  server_de:       true,
  mobile:          true,
  password:        false,
  beta:            false,
  test:            false,
  sign:            true,
  schedule:        'H 17 * * *'
]

if (BRANCH_NAME == 'develop') {
  defaults.putAll([
    branch:          'unstable',
    macos_x86_64:    false,
    macos_x86_64_v8: false,
    macos_arm64:     false,
    android:         false,
    server_ce:       false,
    server_de:       false,
    mobile:          false,
    beta:            true
  ])
}

if (BRANCH_NAME ==~ /^(hotfix|release)\/.+/) {
  defaults.putAll([
    branch:          'testing',
    version:         BRANCH_NAME.replaceAll(/.+\/v(?=[0-9.]+)/,''),
    schedule:        'H 23 * * *'
  ])
}

branding = [
  onlyoffice: true,
  company:    "ONLYOFFICE",
  company_lc: "onlyoffice",
  owner:      "ONLYOFFICE",
  repo:       "onlyoffice",
  winWsDir:   "oo",
]

packages = [
  windows: [
    desktop: [
      srcPrefix: "desktop-apps/win-linux/package/windows",
      destPrefix: "${s3prefix}/windows/${version}/desktop",
      items: [
        [section: "Installer",  glob: "*.exe", dest: "/"],
        [section: "Installer",  glob: "*.msi", dest: "/"],
        [section: "Portable",   glob: "*.zip", dest: "/"],
        [section: "WinSparkle",
         glob: "update/*.exe,update/*.xml,update/*.html", dest: "/"]
      ]
    ],
    builder: [
      srcPrefix: "document-builder-package",
      destPrefix: "${s3prefix}/windows/${version}/builder",
      items: [
        [section: "Installer", glob: "exe/*.exe", dest: "/"],
        [section: "Portable",  glob: "zip/*.zip", dest: "/"]
      ]
    ],
    server: [
      srcPrefix: "document-server-package",
      destPrefix: "${s3prefix}/windows/${version}/server",
      items: [
        [section: "Installer", glob: "exe/*.exe", dest: "/"]
      ]
    ]
  ],
  macos: [
    desktop: [
      srcPrefix: "desktop-apps/macos/build",
      destPrefix: "${s3prefix}/macos/${version}/${suffix}",
      items: [
        [section: "Disk Image", glob: "*.dmg", dest: "/"],
        [section: "Sparkle",
         glob: "${branding.company}-${suffix}-*.zip,update/*.delta,update/*.xml,update/*.html",
         dest: "/"]
      ]
    ]
  ],
  linux: [
    desktop: [
      srcPrefix: "desktop-apps/win-linux/package/linux",
      destPrefix: s3prefix,
      items: [
        [section: "Ubuntu",   glob: "deb/*.deb",        dest: "/ubuntu/"  ],
        [section: "CentOS",   glob: "rpm/**/*.rpm",     dest: "/centos/"  ],
        [section: "AltLinux", glob: "apt-rpm/**/*.rpm", dest: "/altlinux/"],
        [section: "Rosa",     glob: "urpmi/**/*.rpm",   dest: "/rosa/"    ],
        [section: "Portable", glob: "tar/**/*.tar.gz",  dest: "/linux/"   ],
        // [section: "AstraLinux Signed", glob: "deb-astra/*.deb", dest: "/astralinux/"]
      ]
    ],
    builder: [
      srcPrefix: "document-builder-package",
      destPrefix: s3prefix,
      items: [
        [section: "Ubuntu",   glob: "deb/*.deb",    dest: "/ubuntu/"],
        [section: "CentOS",   glob: "rpm/**/*.rpm", dest: "/centos/"],
        [section: "Portable", glob: "tar/*.tar.gz", dest: "/linux/" ]
      ]
    ],
    server: [
      srcPrefix: "document-server-package",
      destPrefix: s3prefix,
      items: [
        [section: "Ubuntu",   glob: "deb/*.deb",        dest: "/ubuntu/"  ],
        [section: "CentOS",   glob: "rpm/**/*.rpm",     dest: "/centos/"  ],
        [section: "AltLinux", glob: "apt-rpm/**/*.rpm", dest: "/altlinux/"],
        [section: "Portable", glob: "*.tar.gz",         dest: "/linux/"   ]
      ]
    ]
  ]
]

pipeline {
  agent none
  environment {
    COMPANY_NAME = "ONLYOFFICE"
    RELEASE_BRANCH = "${defaults.branch}"
    PRODUCT_VERSION = "${defaults.version}"
    TELEGRAM_TOKEN = credentials('telegram-bot-token')
    S3_BUCKET = "repo-doc-onlyoffice-com"
  }
  options {
    checkoutToSubdirectory 'documents-pipeline'
    buildDiscarder logRotator(daysToKeepStr: '30', artifactDaysToKeepStr: '30')
  }
  parameters {
    booleanParam (
      name:         'wipe',
      description:  'Wipe out current workspace',
      defaultValue: false
    )
    booleanParam (
      name:         'clean',
      description:  'Rebuild binaries from the \'core\' repo',
      defaultValue: defaults.clean
    )
    // Windows
    booleanParam (
      name:         'windows_x64',
      description:  'Build Windows x64 targets (Visual Studio 2019)',
      defaultValue: defaults.windows_x64
    )
    booleanParam (
      name:         'windows_x86',
      description:  'Build Windows x86 targets (Visual Studio 2019)',
      defaultValue: defaults.windows_x86
    )
    booleanParam (
      name:         'windows_x64_xp',
      description:  'Build Windows XP x64 targets (Visual Studio 2015)',
      defaultValue: defaults.windows_x64_xp
    )
    booleanParam (
      name:         'windows_x86_xp',
      description:  'Build Windows XP x86 targets (Visual Studio 2015)',
      defaultValue: defaults.windows_x86_xp
    )
    // macOS
    booleanParam (
      name:         'macos_x86_64',
      description:  'Build macOS x86-64 targets',
      defaultValue: defaults.macos_x86_64
    )
    booleanParam (
      name:         'macos_x86_64_v8',
      description:  'Build macOS V8 x86-64 targets',
      defaultValue: defaults.macos_x86_64_v8
    )
    booleanParam (
      name:         'macos_arm64',
      description:  'Build macOS ARM64 targets',
      defaultValue: defaults.macos_arm64
    )
    // Linux
    booleanParam (
      name:         'linux_x86_64',
      description:  'Build Linux x86-64 targets',
      defaultValue: defaults.linux_x86_64
    )
    booleanParam (
      name:         'linux_aarch64',
      description:  'Build Linux aarch64 targets',
      defaultValue: defaults.linux_aarch64
    )
    // Android
    booleanParam (
      name:         'android',
      description:  'Build Android targets',
      defaultValue: defaults.android
    )
    // Modules
    booleanParam (
      name:         'core',
      description:  'Build and publish "core" binaries',
      defaultValue: defaults.core
    )
    booleanParam (
      name:         'desktop',
      description:  'Build and publish DesktopEditors packages',
      defaultValue: defaults.desktop
    )
    booleanParam (
      name:         'builder',
      description:  'Build and publish DocumentBuilder packages',
      defaultValue: defaults.builder
    )
    booleanParam (
      name:         'server_ce',
      description:  'Build and publish DocumentServer packages',
      defaultValue: defaults.server_ce
    )
    booleanParam (
      name:         'server_ee',
      description:  'Build and publish DocumentServer-EE packages',
      defaultValue: defaults.server_ee
    )
    booleanParam (
      name:         'server_de',
      description:  'Build and publish DocumentServer-DE packages',
      defaultValue: defaults.server_de
    )
    booleanParam (
      name:         'mobile',
      description:  'Build and publish Mobile libraries',
      defaultValue: defaults.mobile
    )
    // Other
    /*
    booleanParam (
      name:         'password_protection',
      description:  'Enable password protection',
      defaultValue: defaults.password
    )
    */
    booleanParam (
      name:         'beta',
      description:  'Beta (enabled anyway on develop)',
      defaultValue: defaults.beta
    )
    booleanParam (
      name:         'test',
      description:  'Run test (Only on Linux)',
      defaultValue: defaults.test
    )
    booleanParam (
      name:         'signing',
      description:  'Sign installer (Only on Windows)',
      defaultValue: defaults.sign
    )
    string (
      name:         'extra_args',
      description:  'configure.py extra args',
      defaultValue: ''
    )
    booleanParam (
      name:         'notify',
      description:  'Telegram notification',
      defaultValue: true
    )
  }
  triggers {
    cron(defaults.schedule)
  }
  stages {
    stage('Prepare') {
      steps {
        script {
          if (params.signing) env.ENABLE_SIGNING=1
          s3region = "eu-west-1"
          s3bucket = "repo-doc-onlyoffice-com"
          s3prefix = "${s3bucket}/${branding.company_lc}/${env.RELEASE_BRANCH}"
          branchDir = env.BRANCH_NAME.replaceAll(/\//,'_')
          platforms = [
            windows_x64:     [title: "Windows x64",     arch: "x64",   build: "win_64",      isUnix: false],
            windows_x64_xp:  [title: "Windows XP x64",  arch: "x64",   build: "win_64_xp",   isUnix: false],
            windows_x86:     [title: "Windows x86",     arch: "x86",   build: "win_32",      isUnix: false],
            windows_x86_xp:  [title: "Windows XP x86",  arch: "x86",   build: "win_32_xp",   isUnix: false],
            macos_x86_64:    [title: "macOS x86_64",    arch: "x64",   build: "mac_64",      isUnix: true ],
            macos_x86_64_v8: [title: "macOS V8 x86_64", arch: "x64",   build: "mac_64",      isUnix: true ],
            macos_arm64:     [title: "macOS ARM64",     arch: "arm64", build: "mac_arm64",   isUnix: true ],
            linux_x86_64:    [title: "Linux x86_64",    arch: "x64",   build: "linux_64",    isUnix: true ],
            linux_aarch64:   [title: "Linux aarch64",   arch: "arm64", build: "linux_arm64", isUnix: true ],
            android:         [title: "Android",         arch: "arm",   build: "android",     isUnix: true ]
          ]
          listDeploy = []
          stageStats = [:]
        }
      }
    }
    stage('Build') {
      parallel {
        // Windows
        stage('Windows x64') {
          agent {
            node {
              label 'windows_x64'
              customWorkspace "C:\\${branding.winWsDir}\\${branchDir}_x64"
            }
          }
          when {
            expression { params.windows_x64 }
            beforeAgent true
          }
          steps {
            script {
              echo "NODE_NAME=" + env.NODE_NAME
              stageStats."${env.STAGE_NAME}" = false

              if (params.wipe)
                deleteDir()
              else if (params.clean && params.desktop)
                dir ('desktop-apps') { deleteDir() }

              String platform = "windows_x64"
              ArrayList varRepos = getVarRepos(env.BRANCH_NAME, platform, branding.repo)
              checkoutRepos(varRepos)

              if (params.core || params.builder || params.server_ce) {
                buildArtifacts(platform)
                if (params.core)      buildCore(platform)
                if (params.builder)   buildBuilder(platform)
                if (params.server_ce) buildServer(platform)
              }

              if (params.desktop || params.server_ee || params.server_de) {
                buildArtifacts(platform, "commercial")
                if (params.desktop)   buildDesktop(platform)
                if (params.server_ee) buildServer(platform, "enterprise")
                if (params.server_de) buildServer(platform, "developer")
              }

              stageStats."${env.STAGE_NAME}" = true
            }
          }
        }
        stage('Windows x86') {
          agent {
            node {
              label 'windows_x86'
              customWorkspace "C:\\${branding.winWsDir}\\${branchDir}_x86"
            }
          }
          when {
            expression { params.windows_x86 }
            beforeAgent true
          }
          environment {
            UNAME_M = 'i686'
          }
          steps {
            script {
              echo "NODE_NAME=" + env.NODE_NAME
              stageStats."${env.STAGE_NAME}" = false

              if (params.wipe)
                deleteDir()
              else if (params.clean && params.desktop)
                dir ('desktop-apps') { deleteDir() }

              String platform = "windows_x86"
              ArrayList varRepos = getVarRepos(env.BRANCH_NAME, platform, branding.repo)
              checkoutRepos(varRepos)

              if (params.core) {
                buildArtifacts(platform)
                buildCore(platform)
              }

              if (params.desktop) {
                buildArtifacts(platform, "commercial")
                buildDesktop(platform)
              }

              stageStats."${env.STAGE_NAME}" = true
            }
          }
        }
        stage('Windows XP x64') {
          agent {
            node {
              label 'windows_x64_xp'
              customWorkspace "C:\\${branding.winWsDir}\\${branchDir}_x64_xp"
            }
          }
          when {
            expression { params.windows_x64_xp }
            beforeAgent true
          }
          environment {
            _WIN_XP = '1'
          }
          steps {
            script {
              echo "NODE_NAME=" + env.NODE_NAME
              stageStats."${env.STAGE_NAME}" = false

              if (params.wipe)
                deleteDir()
              else if (params.clean && params.desktop)
                dir ('desktop-apps') { deleteDir() }

              String platform = "windows_x64_xp"
              ArrayList varRepos = getVarRepos(env.BRANCH_NAME, platform, branding.repo)
              checkoutRepos(varRepos)

              if (params.desktop) {
                buildArtifacts(platform, "commercial")
                buildDesktop(platform)
              }

              stageStats."${env.STAGE_NAME}" = true
            }
          }
        }
        stage('Windows XP x86') {
          agent {
            node {
              label 'windows_x86_xp'
              customWorkspace "C:\\${branding.winWsDir}\\${branchDir}_x86_xp"
            }
          }
          when {
            expression { params.windows_x86_xp }
            beforeAgent true
          }
          environment {
            _WIN_XP = '1'
            UNAME_M = 'i686'
          }
          steps {
            script {
              echo "NODE_NAME=" + env.NODE_NAME
              stageStats."${env.STAGE_NAME}" = false

              if (params.wipe)
                deleteDir()
              else if (params.clean && params.desktop)
                dir ('desktop-apps') { deleteDir() }

              String platform = "windows_x86_xp"
              ArrayList varRepos = getVarRepos(env.BRANCH_NAME, platform, branding.repo)
              checkoutRepos(varRepos)

              if (params.desktop) {
                buildArtifacts(platform, "commercial")
                buildDesktop(platform)
              }

              stageStats."${env.STAGE_NAME}" = true
            }
          }
        }
        // macOS
        stage('macOS x86_64') {
          agent { label 'macos_x86_64' }
          environment {
            FASTLANE_HIDE_TIMESTAMP = '1'
            FASTLANE_SKIP_UPDATE_CHECK = '1'
            APPLE_ID = credentials('macos-apple-id')
            TEAM_ID = credentials('macos-team-id')
            FASTLANE_APPLE_APPLICATION_SPECIFIC_PASSWORD = credentials('macos-apple-password')
            CODESIGNING_IDENTITY = 'Developer ID Application'
          }
          when {
            expression { params.macos_x86_64 }
            beforeAgent true
          }
          steps {
            script {
              echo "NODE_NAME=" + env.NODE_NAME
              stageStats."${env.STAGE_NAME}" = false

              if (params.wipe)
                deleteDir()
              else if (params.clean && params.desktop)
                dir ('desktop-apps') { deleteDir() }

              String platform = "macos_x86_64"
              ArrayList varRepos = getVarRepos(env.BRANCH_NAME, platform, branding.repo)
              checkoutRepos(varRepos)

              if (params.core) {
                buildArtifacts(platform)
                buildCore(platform)
              }

              if (params.desktop) {
                buildArtifacts(platform, "commercial")
                buildDesktop(platform)
              }

              stageStats."${env.STAGE_NAME}" = true
            }
          }
        }
        stage('macOS V8 x86_64') {
          agent { label 'macos_x86_64_v8' }
          environment {
            FASTLANE_HIDE_TIMESTAMP = '1'
            FASTLANE_SKIP_UPDATE_CHECK = '1'
            APPLE_ID = credentials('macos-apple-id')
            TEAM_ID = credentials('macos-team-id')
            FASTLANE_APPLE_APPLICATION_SPECIFIC_PASSWORD = credentials('macos-apple-password')
            CODESIGNING_IDENTITY = 'Developer ID Application'
            USE_V8 = '1'
          }
          when {
            expression { params.macos_x86_64_v8 }
            beforeAgent true
          }
          steps {
            script {
              echo "NODE_NAME=" + env.NODE_NAME
              stageStats."${env.STAGE_NAME}" = false

              if (params.wipe)
                deleteDir()
              else if (params.clean && params.desktop)
                dir ('desktop-apps') { deleteDir() }

              String platform = "macos_x86_64_v8"
              ArrayList varRepos = getVarRepos(env.BRANCH_NAME, platform, branding.repo)
              checkoutRepos(varRepos)

              if (params.desktop) {
                buildArtifacts(platform, "commercial")
                buildDesktop(platform)
              }

              stageStats."${env.STAGE_NAME}" = true              
            }
          }
        }
        stage('macOS ARM64') {
          agent { label 'macos_arm64' }
          environment {
            FASTLANE_HIDE_TIMESTAMP = '1'
            FASTLANE_SKIP_UPDATE_CHECK = '1'
            APPLE_ID = credentials('macos-apple-id')
            TEAM_ID = credentials('macos-team-id')
            FASTLANE_APPLE_APPLICATION_SPECIFIC_PASSWORD = credentials('macos-apple-password')
            CODESIGNING_IDENTITY = 'Developer ID Application'
          }
          when {
            expression { params.macos_arm64 }
            beforeAgent true
          }
          steps {
            script {
              echo "NODE_NAME=" + env.NODE_NAME
              stageStats."${env.STAGE_NAME}" = false

              if (params.wipe)
                deleteDir()
              else if (params.clean && params.desktop)
                dir ('desktop-apps') { deleteDir() }

              String platform = "macos_arm64"
              ArrayList varRepos = getVarRepos(env.BRANCH_NAME, platform, branding.repo)
              checkoutRepos(varRepos)

              if (params.desktop) {
                buildArtifacts(platform, "commercial")
                buildDesktop(platform)
              }

              stageStats."${env.STAGE_NAME}" = true              
            }
          }
        }
        // Linux
        stage('Linux x86_64') {
          agent { label 'linux_x86_64' }
          when {
            expression { params.linux_x86_64 }
            beforeAgent true
          }
          steps {
            script {
              echo "NODE_NAME=" + env.NODE_NAME
              stageStats."${env.STAGE_NAME}" = false

              if (params.wipe)
                deleteDir()
              else if (params.clean && params.desktop)
                dir ('desktop-apps') { deleteDir() }

              String platform = "linux_x86_64"
              ArrayList constRepos = getConstRepos(env.BRANCH_NAME)
              ArrayList varRepos = getVarRepos(env.BRANCH_NAME, platform, branding.repo)
              ArrayList allRepos = constRepos.plus(varRepos)
              checkoutRepos(varRepos)

              if (params.core || params.builder || params.server_ce) {
                buildArtifacts(platform)
                if (params.core)      buildCore(platform)
                if (params.builder)   buildBuilder(platform)
                if (params.server_ce) buildServer(platform)
              }

              if (params.desktop || params.server_ee || params.server_de) {
                buildArtifacts(platform, "commercial")
                if (params.desktop)   buildDesktop(platform)
                if (params.server_ee) {
                  buildServer(platform, "enterprise")
                  tagRepos(allRepos, "v${env.PRODUCT_VERSION}.${env.BUILD_NUMBER}")
                }
                if (params.server_de) buildServer(platform, "developer")
              }
              if (params.test) linuxTest()

              stageStats."${env.STAGE_NAME}" = true
            }
          }
        }
        stage('Linux aarch64') {
          agent { label 'linux_aarch64' }
          when {
            expression { params.linux_aarch64 }
            beforeAgent true
          }
          steps {
            script {
              echo "NODE_NAME=" + env.NODE_NAME
              stageStats."${env.STAGE_NAME}" = false

              if (params.wipe)
                deleteDir()
              else if (params.clean && params.desktop)
                dir ('desktop-apps') { deleteDir() }

              String platform = "linux_aarch64"
              ArrayList constRepos = getConstRepos(env.BRANCH_NAME)
              ArrayList varRepos = getVarRepos(env.BRANCH_NAME, platform, branding.repo)
              ArrayList allRepos = constRepos.plus(varRepos)
              checkoutRepos(varRepos)

              if (params.core || params.builder || params.server_ce) {
                buildArtifacts(platform)
                if (params.builder)   buildBuilder(platform)
                if (params.server_ce) buildServer(platform)
              }

              if (params.desktop || params.server_ee || params.server_de) {
                buildArtifacts(platform, "commercial")
                // if (params.desktop)   buildDesktop(platform)
                if (params.server_ee) buildServer(platform, "enterprise")
                if (params.server_de) buildServer(platform, "developer")
              }

              stageStats."${env.STAGE_NAME}" = true
            }
          }
        }
        // Android
        stage('Android') {
          agent { label 'android' }
          when {
            expression { params.android && params.mobile }
            beforeAgent true
          }
          steps {
            script {
              echo "NODE_NAME=" + env.NODE_NAME
              stageStats."${env.STAGE_NAME}" = false

              if (params.wipe) deleteDir()

              String platform = "android"
              ArrayList varRepos = getVarRepos(env.BRANCH_NAME, platform, null)
              checkoutRepos(varRepos)

              buildArtifacts(platform)
              buildAndroid(env.BRANCH_NAME)

              stageStats."${env.STAGE_NAME}" = true
            }
          }
        }
      }
    }
  }
  post {
    always {
      node('built-in') {
        script {
          generateReports()
        }
      }
      script {
        if (params.linux_x86_64 || params.linux_aarch64)
          build (
            job: 'repo-manager',
            parameters: [
              string (name: 'company', value: branding.company_lc),
              string (name: 'branch', value: env.RELEASE_BRANCH)
            ],
            wait: false
          )
      }
    }
    fixed {
      node('built-in') {
        script {
          sendTelegramMessage(getJobStats('fixed'), '-1001773122025')
        }
      }
    }
    failure {
      node('built-in') {
        script {
          sendTelegramMessage(getJobStats('failed'), '-1001773122025')
        }
      }
    }
  }
}

void checkoutRepo(String repo, String branch = 'master', String dir = repo.minus(~/^.+\//)) {
  if (dir == null) dir = repo.minus(~/^.+\//)
  def retryCount = 0
  retry(3) {
    if (retryCount > 0) sleep(30)
    checkout([
      $class: 'GitSCM',
      branches: [[name: 'refs/heads/' + branch]],
      doGenerateSubmoduleConfigurations: false,
      extensions: [
        [$class: 'SubmoduleOption', recursiveSubmodules: true],
        [$class: 'RelativeTargetDirectory', relativeTargetDir: dir],
        [$class: 'ScmName', name: "${repo}"]
      ],
      submoduleCfg: [],
      userRemoteConfigs: [[url: "git@github.com:${repo}.git"]]
    ])
    retryCount++
  }
}

def getConstRepos(String branch = 'master') {
  return [
    [owner: "ONLYOFFICE",   name: "build_tools"],
    [owner: branding.owner, name: branding.repo]
  ].each {
    it.branch = branch
    it.dir = it.name
  }
}

def getVarRepos(String branch, String platform, String branding) {
  checkoutRepos(getConstRepos(branch))

  String modules = getModules(platforms[platform].build).join(' ')
  String scriptArgs = ""
  if (!modules.isEmpty()) scriptArgs = "--module \"${modules}\""
  if (platform != null) scriptArgs += " --platform \"${platforms[platform].build}\""
  if (branding != null) scriptArgs += " --branding \"${branding}\""

  String reposOutput
  if (platforms[platform].isUnix) {
    reposOutput = sh(
      script: "cd build_tools/scripts/develop && \
        ./print_repositories.py ${scriptArgs}",
      returnStdout: true
    )
  } else {
    reposOutput = powershell(
      script: "cd build_tools\\scripts\\develop; \
        python print_repositories.py ${scriptArgs}",
      returnStdout: true
    )
  }

  ArrayList repos = []
  reposOutput.readLines().sort().each { line ->
    ArrayList lineSplit = line.split(" ")
    Map repo = [
      owner: "ONLYOFFICE",
      name: lineSplit[0],
      branch: "master",
      dir: (lineSplit[1] == null) ? "${lineSplit[0]}" : "${lineSplit[1]}/${lineSplit[0]}"
    ]
    if (branch != 'master') repo.branch = resolveScm(
        source: [
          $class: 'GitSCMSource',
          remote: "git@github.com:${repo.owner}/${repo.name}.git",
          traits: [[$class: 'jenkins.plugins.git.traits.BranchDiscoveryTrait']]
        ],
        targets: [branch, 'master']
      ).branches[0].name

    repos.add(repo)
  }

  return repos.sort()
}

void checkoutRepos(ArrayList repos) {
  echo repos.collect({"${it.owner}/${it.name} (${it.branch})"}).join("\n")
  repos.each {
    checkoutRepo(it.owner + "/" + it.name, it.branch, it.dir)
  }
}

void tagRepos(ArrayList repos, String tag) {
  repos.each {
    sh """
      cd ${it.dir}
      git tag -l | xargs git tag -d
      git fetch --tags
      git tag ${tag}
      git push origin --tags
    """
  }
}

// Build

def getModules(String platform, String license = "any") {
  Boolean isOpenSource = license in ["opensource", "any"]
  Boolean isCommercial = license in ["commercial", "any"]
  Boolean pCore = platform in ["win_64", "win_32", "mac_64", "linux_64", "linux_arm64"]
  Boolean pDesktop = platform != "linux_arm64"
  Boolean pBuilder = platform in ["win_64", "linux_64", "linux_arm64"]
  Boolean pServer = platform in ["win_64", "linux_64", "linux_arm64"]
  Boolean pMobile = platform == "android"

  ArrayList modules = []
  if (params.core && isOpenSource && pCore)
    modules.add("core")
  if (params.desktop && isCommercial && pDesktop)
    modules.add("desktop")
  if (params.builder && isOpenSource && pBuilder)
    modules.add("builder")
  if ((((params.server_de || params.server_ee) && isCommercial) \
    || (params.server_ce && isOpenSource)) && pServer)
    modules.add("server")
  if (params.mobile && isOpenSource && pMobile)
    modules.add("mobile")

  return modules
}

def getConfigArgs(String platform = 'native', String license = 'opensource') {
  ArrayList modules = getModules(platform, license)
  if (platform.startsWith("win")) modules.add("tests")

  ArrayList args = []
  args.add("--module \"${modules.join(' ')}\"")
  args.add("--platform \"${platform}\"")
  args.add("--update false")
  args.add("--clean ${params.clean.toString()}")
  args.add("--qt-dir ${env.QT_PATH}")
  if (platform in ["win_64_xp", "win_32_xp"])
    args.add("--qt-dir-xp ${env.QT56_PATH}")
  if (license == "commercial")
    args.add("--branding ${branding.repo}")
  if (platform in ["win_64", "win_32"])
    args.add("--vs-version 2019")
  if (platform == "mac_64" && env.USE_V8 == "1")
    args.add("--config use_v8")
  if (platform == "android")
    args.add("--config release")
  // if (params.password_protection)
  //   args.add("--features \"enable_protection disable_signatures\"")
  if (params.beta)
    args.add("--beta 1")
  if (!params.extra_args.isEmpty())
    args.add(params.extra_args)

  return args.join(' ')
}

void buildArtifacts(String platform, String license = 'opensource') {
  if (platforms[platform].isUnix) {
    sh "cd build_tools && \
      ./configure.py ${getConfigArgs(platforms[platform].build, license)} && \
      ./make.py"
  } else {
    bat "cd build_tools && \
      call python configure.py ${getConfigArgs(platforms[platform].build, license)} && \
      call python make.py"
  }
}

// Build Packages

void buildCore(String platform) {
  String branch = env.BRANCH_NAME
  String version = env.PRODUCT_VERSION
  String build = env.BUILD_NUMBER
  LinkedHashMap path = [
    windows_x64:  [os: "windows", version: "${version}.${build}", arch: "x64"],
    windows_x86:  [os: "windows", version: "${version}.${build}", arch: "x86"],
    macos_x86_64: [os: "macos",   version: "${version}-${build}", arch: "x64"],
    linux_x86_64: [os: "linux",   version: "${version}-${build}", arch: "x64"]
  ]
  def p = path[platform]
  Closure coreDeployPath = {
    return "${s3bucket}/${p.os}/core/${branch}/${it}/${p.arch}"
  }
  String uploadCmd = """
    aws s3 cp --acl public-read --no-progress \
      build_tools/out/${platforms[platform].build}/${branding.company_lc}/core/core.7z \
      s3://${coreDeployPath(p.version)}/core.7z
    aws s3 sync --delete --acl public-read --no-progress \
      s3://${coreDeployPath(p.version)}/ \
      s3://${coreDeployPath('latest')}/
  """

  if (platforms[platform].isUnix) sh uploadCmd else powershell uploadCmd
}

void buildDesktop(String platform) {
  String version = "${env.PRODUCT_VERSION}-${env.BUILD_NUMBER}"
  String makeargs = ""

  if (platform ==~ /^windows.*/) {

    ArrayList targets = ['clean']
    if (platform == 'windows_x64') {
      targets += ['innosetup-x64', 'winsparkle-update', 'winsparkle-files',
                  'advinst-x64', 'portable-zip-x64', 'portable-evb-x64']
    } else if (platform == 'windows_x86') {
      targets += ['innosetup-x86', 'winsparkle-update',
                  'advinst-x86', 'portable-zip-x86', 'portable-evb-x86']
    } else if (platform == 'windows_x64_xp') {
      targets += ['innosetup-x64-xp', 'winsparkle-update', 'portable-zip-x64-xp']      
    } else if (platform == 'windows_x86_xp') {
      targets += ['innosetup-x86-xp', 'winsparkle-update', 'portable-zip-x86-xp']
    }
    if (params.signing) targets += ['sign']
    buildPackages("desktop", platform, targets)
    uploadFiles("desktop", platform,
                packages.windows.desktop.items,
                packages.windows.desktop.srcPrefix,
                packages.windows.desktop.destPrefix)

  } else if (platform ==~ /^macos.*/) {

    String suffix
    ArrayList targets = ['clean']
    if (platform == "macos_x86_64_v8") {
      suffix = "v8"
      targets = ["diskimage-v8-x86_64"]
    } else if (platform == "macos_x86_64") {
      suffix = "x86_64"
      targets = ["diskimage-x86_64"]
    } else if (platform == "macos_arm64") {
      suffix = "arm"
      targets = ["diskimage-arm64"]
    }

    buildPackages("desktop", platform, targets)
    // String appVersion = sh (
    //   script: "mdls -name kMDItemVersion -raw desktop-apps/macos/build/ONLYOFFICE.app",
    //   returnStdout: true).trim()
    uploadFiles("desktop", platform,
                packages.macos.desktop.items,
                packages.macos.desktop.srcPrefix,
                packages.macos.desktop.destPrefix)

  } else if (platform ==~ /^linux.*/) {

    if (!branding.onlyoffice)
      makeargs += " -e BRANDING_DIR=../../../../${branding.repo}/desktop-apps/win-linux/package/linux"
    if (platform == "linux_aarch64")
      makeargs += " -e UNAME_M=aarch64"
    sh "cd desktop-apps/win-linux/package/linux && \
      make clean && \
      make packages ${makeargs}"
    uploadFiles("desktop", platform,
                packages.linux.desktop.items,
                packages.linux.desktop.srcPrefix,
                packages.linux.desktop.destPrefix)

  }
}

void buildBuilder(String platform) {
  String version = "${env.PRODUCT_VERSION}-${env.BUILD_NUMBER}"
  String makeargs = ""

  if (platform ==~ /^windows.*/) {

    ArrayList targets = ['clean']
    if (platform == 'windows_x64') targets += ['innosetup-x64', 'portable-x64']
    if (params.signing) targets += ['sign']
    buildPackages("builder", platform, targets)
    uploadFiles("builder", platform,
                packages.windows.builder.items,
                packages.windows.builder.srcPrefix,
                packages.windows.builder.destPrefix)

  } else if (platform ==~ /^linux.*/) {

    if (platform == "linux_aarch64")
      makeargs += " -e UNAME_M=aarch64"
    if (!branding.onlyoffice)
      makeargs += " -e BRANDING_DIR=../${branding.repo}/document-builder-package"
    sh "cd document-builder-package && \
      make clean && \
      make packages ${makeargs}"
    uploadFiles("builder", platform,
                packages.linux.builder.items,
                packages.linux.builder.srcPrefix,
                packages.linux.builder.destPrefix)

  }
}

void buildServer(String platform, String edition='community') {
  String version = "${env.PRODUCT_VERSION}-${env.BUILD_NUMBER}"
  String product, productName, makeargs = ""

  switch(edition) {
    case "community":
      product = "server_ce"
      productName = "DocumentServer"
      break
    case "enterprise":
      product = "server_ee"
      productName = "DocumentServer-EE"
      break
    case "developer":
      product = "server_de"
      productName = "DocumentServer-DE"
      break
  }

  if (platform ==~ /^windows.*/) {

    makeargs = "-e PRODUCT_NAME=${productName}"
    if (!branding.onlyoffice)
      makeargs += " -e BRANDING_DIR=../${branding.repo}/document-server-package"
    bat "cd document-server-package && \
      make clean && \
      make packages ${makeargs}"
    uploadFiles(product, platform,
                packages.windows.server.items,
                packages.windows.server.srcPrefix,
                packages.windows.server.destPrefix)

  } else if (platform ==~ /^linux.*/) {

    makeargs = "-e PRODUCT_NAME=${productName.toLowerCase()}"
    if (platform == "linux_aarch64")
      makeargs += " -e UNAME_M=aarch64"
    if (!branding.onlyoffice)
      makeargs += " -e BRANDING_DIR=../${branding.repo}/document-server-package"
    sh "cd document-server-package && \
      make clean && \
      make packages ${makeargs}"
    uploadFiles(product, platform,
                packages.linux.server.items,
                packages.linux.server.srcPrefix,
                packages.linux.server.destPrefix)

    if (platform == "linux_x86_64") {
      makeargs = "-e PRODUCT_NAME=${productName.toLowerCase()}"
      if (!branding.onlyoffice)
        makeargs += " -e ONLYOFFICE_VALUE=ds"
      sh "cd Docker-DocumentServer && \
        make clean && \
        make deploy ${makeargs}"
    }
  }
}

void buildAndroid(String branch = 'master', String config = 'release') {
  String version = "${env.PRODUCT_VERSION}-${env.BUILD_NUMBER}"

  sh "rm -rfv *.zip && \
    cd build_tools/out && \
    zip -r ../../android-libs-${version}.zip ./android* ./js"
  uploadFiles("mobile", "android", [
      [section: "Libs", glob: "*.zip", dest: "/android/"],
    ], "", s3prefix)
}

void buildPackages(String product, String platform, ArrayList targets) {
  String args = "--product ${product}" \
    + " --version ${env.PRODUCT_VERSION}" \
    + " --build ${env.BUILD_NUMBER}" \
    + " --targets ${targets.join(' ')}"
  if (!branding.onlyoffice)
    args += " --branding ${branding.repo}"
  if (platforms[platform].isUnix)
    sh "cd build_tools && ./make_package.py ${args}"
  else
    bat "cd build_tools && call python make_package.py ${args}"
}

// Upload

void uploadFiles(String product, String platform, ArrayList items, \
                 String srcPrefix = '', String destPrefix = '') {
  String srcPath, uploadCmd = ""
  ArrayList localListDeploy = []

  Closure cmdMd5sum = {
    if (platform ==~ /^windows.*/) {
      return powershell (
        script: "Get-FileHash ${it} -Algorithm MD5 | Select -ExpandProperty Hash",
        returnStdout: true).trim()
    } else if (platform ==~ /^macos.*/) {
      return sh (script: "md5 -qs ${it}", returnStdout: true).trim()
    } else {
      return sh (script: "md5sum ${it} | cut -c -32", returnStdout: true).trim()
    }
  }

  dir(srcPrefix) {
    items.each { item ->
      findFiles(glob: item.glob).each { file ->
        srcPath = "${destPrefix}${item.dest}"
        localListDeploy.add([
          product: product,
          platform: platforms[platform].title,
          section: item.section,
          path: srcPath + file.name,
          file: file.name,
          size: file.length,
          md5: cmdMd5sum(file.path)
          // sha256: cmdSha256sum(it.path)
        ])
        uploadCmd += "aws s3 cp --acl public-read --no-progress " \
          + "${file.path} s3://${srcPath}; "
      }
    }
    if (platforms[platform].isUnix) sh uploadCmd else powershell uploadCmd
  }

  listDeploy.addAll(localListDeploy)
}

// Tests

void linuxTest() {
  checkoutRepo([owner: 'ONLYOFFICE', name: 'doc-builder-testing'], 'master')
  sh "docker rmi doc-builder-testing || true"
  sh "cd doc-builder-testing && \
    docker build --tag doc-builder-testing -f dockerfiles/debian-develop/Dockerfile . &&\
    docker run --rm doc-builder-testing bundle exec parallel_rspec spec -n 2"
}

// Reports

void generateReports() {
  Map deploy = listDeploy.groupBy { it.product }

  Boolean desktop = deploy.desktop != null
  Boolean builder = deploy.builder != null
  Boolean server_ce = deploy.server_ce != null
  Boolean server_ee = deploy.server_ee != null
  Boolean server_de = deploy.server_de != null
  Boolean mobile = deploy.mobile != null

  deleteDir()
  if (desktop)
    publishReport("DesktopEditors", ["desktop.html": deploy.desktop])
  if (builder)
    publishReport("DocumentBuilder", ["builder.html": deploy.builder])
  if (server_ce || server_ee || server_de) {
    Map serverReports = [:]
    if (server_ce) serverReports."server_ce.html" = deploy.server_ce
    if (server_ee) serverReports."server_ee.html" = deploy.server_ee
    if (server_de) serverReports."server_de.html" = deploy.server_de
    publishReport("DocumentServer", serverReports)
  }
  if (mobile)
    publishReport("Mobile", ["mobile.html": deploy.mobile])
}

void publishReport(String title, Map files) {
  files.each {
    writeFile file: it.key, text: getHtml(it.value)
  }
  publishHTML([
    allowMissing: false,
    alwaysLinkToLastBuild: true,
    includes: files.collect({ it.key }).join(','),
    keepAll: true,
    reportDir: '',
    reportFiles: files.collect({ it.key }).join(','),
    reportName: title,
    reportTitles: ''
  ])
}

def getHtml(ArrayList data) {
  String text, url
  Closure size = {
    return sh (script: "LANG=C numfmt --to=iec-i ${it}", returnStdout: true).trim()
  }

  text = "<html>\n<head>" \
    + "\n  <link rel=\"stylesheet\" href=\"https://unpkg.com/style.css\">" \
    + "\n  <style type=\"text/css\">body { margin: 24px; }</style>" \
    + "\n<head>\n<body>"
  data.groupBy { it.platform }.sort().each { platform, sections ->
    text += "\n  <h3>${platform}</h3>\n  <ul>"
    sections.groupBy { it.section }.each { section, files ->
      text += "\n    <li><b>${section}</b></li>\n    <ul>"
      files.each {
        url = "https://s3.${s3region}.amazonaws.com/${it.path}"
        text += "\n      <li>" \
          + "\n        <a href=\"${url}\">${it.file}</a>" \
          + ", Size: ${size(it.size)}B" \
          + ", MD5: <code>${it.md5}</code>" \
          + "\n      </li>"
      }
      text += "\n    </ul>"
    }
    text += "\n  </ul>"
  }
  text += "\n</body>\n</html>"

  return text
}

// Notifications

def getJobStats(String jobStatus) {
  String text = "Build [${currentBuild.fullDisplayName}]" \
    + "(${currentBuild.absoluteUrl}) ${jobStatus}"
  stageStats.sort().each { stage, status ->
    text += "\n${status ? '🔵' : '🔴'} ${stage.replaceAll('_','\\\\_')}"
  }
  return text
}

void sendTelegramMessage(String text, String chatId, Boolean markdown = true) {
  if (params.notify) sh(
    label: "Send Telegram Message",
    script: "curl -X POST -s -S \
      -d chat_id=${chatId} \
      ${markdown ? '-d parse_mode=markdown' : ''} \
      -d disable_web_page_preview=true \
      --data-urlencode text='${text}' \
      https://api.telegram.org/bot\$TELEGRAM_TOKEN/sendMessage"
    )
}
