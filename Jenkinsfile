defaults = [
  branch:           'experimental',
  channel:          'other',
  version:          '99.99.99',
  clean:            true,
  windows_x64:      true,
  windows_x86:      true,
  windows_x64_xp:   true,
  windows_x86_xp:   true,
  darwin_x86_64:    true,
  darwin_x86_64_v8: true,
  darwin_arm64:     true,
  linux_x86_64:     true,
  linux_x86_64_u14: true,
  linux_x86_64_u16: true,
  linux_aarch64:    true,
  android:          true,
  core:             true,
  desktop:          true,
  builder:          true,
  server_ce:        true,
  server_ee:        true,
  server_de:        true,
  mobile:           true,
  password:         false,
  beta:             false,
  test:             false,
  sign:             true,
  schedule:         'H 17 * * *'
]

if (BRANCH_NAME == 'develop') {
  defaults.putAll([
    branch:           'unstable',
    channel:          'nightly',
    darwin_x86_64_v8: false,
    android:          false,
    server_ce:        false,
    server_de:        false,
    mobile:           false,
    beta:             true
  ])
}

if (BRANCH_NAME ==~ /^(hotfix|release)\/.+/) {
  defaults.putAll([
    branch:           'testing',
    channel:          'test',
    version:          BRANCH_NAME.replaceAll(/.+\/v(?=[0-9.]+)/,''),
    schedule:         'H 23 * * *'
  ])
}

branding = [
  onlyoffice:  true,
  company:     "ONLYOFFICE",
  company_lc:  "onlyoffice",
  owner:       "ONLYOFFICE",
  repo:        "onlyoffice",
]

platforms = [
  windows_x64:      [title: "Windows x64",     build: "win_64",      isUnix: false],
  windows_x64_xp:   [title: "Windows x64 XP",  build: "win_64_xp",   isUnix: false],
  windows_x86:      [title: "Windows x86",     build: "win_32",      isUnix: false],
  windows_x86_xp:   [title: "Windows x86 XP",  build: "win_32_xp",   isUnix: false],
  darwin_x86_64:    [title: "macOS x86_64",    build: "mac_64",      isUnix: true ],
  darwin_x86_64_v8: [title: "macOS x86_64 V8", build: "mac_64",      isUnix: true ],
  darwin_arm64:     [title: "macOS arm64",     build: "mac_arm64",   isUnix: true ],
  linux_x86_64:     [title: "Linux x86_64",    build: "linux_64",    isUnix: true ],
  linux_x86_64_u14: [title: "Linux x86_64 (Ubuntu 14)", build: "linux_64", isUnix: true ],
  linux_x86_64_u16: [title: "Linux x86_64 (Ubuntu 16)", build: "linux_64", isUnix: true ],
  linux_aarch64:    [title: "Linux aarch64",   build: "linux_arm64", isUnix: true ],
  android:          [title: "Android",         build: "android",     isUnix: true ]
]

pipeline {
  agent none
  environment {
    COMPANY_NAME = "ONLYOFFICE"
    RELEASE_BRANCH = "${defaults.branch}"
    BUILD_CHANNEL = "${defaults.channel}"
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
      description:  'Build Windows x64 XP targets (Visual Studio 2015)',
      defaultValue: defaults.windows_x64_xp
    )
    booleanParam (
      name:         'windows_x86_xp',
      description:  'Build Windows x86 XP targets (Visual Studio 2015)',
      defaultValue: defaults.windows_x86_xp
    )
    // macOS
    booleanParam (
      name:         'darwin_x86_64',
      description:  'Build macOS x86-64 targets',
      defaultValue: defaults.darwin_x86_64
    )
    booleanParam (
      name:         'darwin_x86_64_v8',
      description:  'Build macOS x86-64 V8 targets',
      defaultValue: defaults.darwin_x86_64_v8
    )
    booleanParam (
      name:         'darwin_arm64',
      description:  'Build macOS arm64 targets',
      defaultValue: defaults.darwin_arm64
    )
    // Linux
    /*
    booleanParam (
      name:         'linux_x86_64_u14',
      description:  'Build Linux x86-64 (Ubuntu 14) targets',
      defaultValue: defaults.linux_x86_64_u14
    )
    */
    booleanParam (
      name:         'linux_x86_64',
      description:  'Build Linux x86-64 (Ubuntu 16) targets',
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
          branchDir = env.BRANCH_NAME.replaceAll(/\//,'_')
          gitTag = "v${env.PRODUCT_VERSION}.${env.BUILD_NUMBER}"
          deployData = []
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
              customWorkspace "C:\\oo\\${branchDir}_x64"
            }
          }
          when {
            expression { params.windows_x64 }
            beforeAgent true
          }
          steps {
            initializeWindows("windows_x64")
          }
          post {
            success  { setStageStats(0) }
            unstable { setStageStats(1) }
            failure  { setStageStats(2) }
          }
        }
        stage('Windows x86') {
          agent {
            node {
              label 'windows_x86'
              customWorkspace "C:\\oo\\${branchDir}_x86"
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
            initializeWindows("windows_x86")
          }
          post {
            success  { setStageStats(0) }
            unstable { setStageStats(1) }
            failure  { setStageStats(2) }
          }
        }
        stage('Windows x64 XP') {
          agent {
            node {
              label 'windows_x64_xp'
              customWorkspace "C:\\oo\\${branchDir}_x64_xp"
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
            initializeWindows("windows_x64_xp")
          }
          post {
            success  { setStageStats(0) }
            unstable { setStageStats(1) }
            failure  { setStageStats(2) }
          }
        }
        stage('Windows x86 XP') {
          agent {
            node {
              label 'windows_x86_xp'
              customWorkspace "C:\\oo\\${branchDir}_x86_xp"
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
            initializeWindows("windows_x86_xp")
          }
          post {
            success  { setStageStats(0) }
            unstable { setStageStats(1) }
            failure  { setStageStats(2) }
          }
        }
        // macOS
        stage('macOS x86_64') {
          agent { label 'darwin_x86_64' }
          when {
            expression { params.darwin_x86_64 }
            beforeAgent true
          }
          environment {
            FASTLANE_HIDE_TIMESTAMP = '1'
            FASTLANE_SKIP_UPDATE_CHECK = '1'
            APPLE_ID = credentials('macos-apple-id')
            TEAM_ID = credentials('macos-team-id')
            FASTLANE_APPLE_APPLICATION_SPECIFIC_PASSWORD = credentials('macos-apple-password')
            CODESIGNING_IDENTITY = 'Developer ID Application'
          }
          steps {
            initializeDarwin("darwin_x86_64")
          }
          post {
            success  { setStageStats(0) }
            unstable { setStageStats(1) }
            failure  { setStageStats(2) }
          }
        }
        stage('macOS x86_64 V8') {
          agent { label 'darwin_x86_64_v8' }
          when {
            expression { params.darwin_x86_64_v8 }
            beforeAgent true
          }
          environment {
            FASTLANE_HIDE_TIMESTAMP = '1'
            FASTLANE_SKIP_UPDATE_CHECK = '1'
            APPLE_ID = credentials('macos-apple-id')
            TEAM_ID = credentials('macos-team-id')
            FASTLANE_APPLE_APPLICATION_SPECIFIC_PASSWORD = credentials('macos-apple-password')
            CODESIGNING_IDENTITY = 'Developer ID Application'
            USE_V8 = '1'
          }
          steps {
            initializeDarwin("darwin_x86_64_v8")
          }
          post {
            success  { setStageStats(0) }
            unstable { setStageStats(1) }
            failure  { setStageStats(2) }
          }
        }
        stage('macOS arm64') {
          agent { label 'darwin_arm64' }
          when {
            expression { params.darwin_arm64 }
            beforeAgent true
          }
          environment {
            FASTLANE_HIDE_TIMESTAMP = '1'
            FASTLANE_SKIP_UPDATE_CHECK = '1'
            APPLE_ID = credentials('macos-apple-id')
            TEAM_ID = credentials('macos-team-id')
            FASTLANE_APPLE_APPLICATION_SPECIFIC_PASSWORD = credentials('macos-apple-password')
            CODESIGNING_IDENTITY = 'Developer ID Application'
          }
          steps {
            initializeDarwin("darwin_arm64")
          }
          post {
            success  { setStageStats(0) }
            unstable { setStageStats(1) }
            failure  { setStageStats(2) }
          }
        }
        // Linux
        /*
        stage('Linux x86_64 (Ubuntu 14)') {
          agent { label 'linux_x86_64_u14' }
          when {
            expression { params.linux_x86_64_u14 }
            beforeAgent true
          }
          environment {
            GITHUB_TOKEN = credentials('github-token')
            DISTRIB_RELEASE = '14.04'
          }
          steps {
            initializeLinux("linux_x86_64_u14")
          }
          post {
            success  { setStageStats(0) }
            unstable { setStageStats(1) }
            failure  { setStageStats(2) }
          }
        }
        */
        stage('Linux x86_64 (Ubuntu 16)') {
          agent { label 'linux_x86_64_u16' }
          when {
            expression { params.linux_x86_64 }
            beforeAgent true
          }
          environment {
            GITHUB_TOKEN = credentials('github-token')
            DISTRIB_RELEASE = '16.04'
          }
          steps {
            initializeLinux("linux_x86_64")
            // initializeLinux("linux_x86_64_u16")
          }
          post {
            success  { setStageStats(0) }
            unstable { setStageStats(1) }
            failure  { setStageStats(2) }
          }
        }
        stage('Linux aarch64 (Ubuntu 16)') {
          agent { label 'linux_aarch64' }
          when {
            expression { params.linux_aarch64 }
            beforeAgent true
          }
          environment {
            DISTRIB_RELEASE = '16.04'
          }
          steps {
            initializeLinux("linux_aarch64")
          }
          post {
            success  { setStageStats(0) }
            unstable { setStageStats(1) }
            failure  { setStageStats(2) }
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
            initializeAndroid()
          }
          post {
            success  { setStageStats(0) }
            unstable { setStageStats(1) }
            failure  { setStageStats(2) }
          }
        }
      }
    }
    stage('Docker') {
      agent { label 'linux_x86_64 || linux_x86_64_u14 || linux_x86_64_u16 || linux_aarch64' }
      environment {
        GITHUB_TOKEN = credentials('github-token')
      }
      when {
        expression {
          params.linux_x86_64 && (params.server_ce || params.server_ee || params.server_de)
        }
        beforeAgent true
      }
      steps {
        script {
          catchError(
            buildResult: 'SUCCESS',
            stageResult: 'FAILURE',
            message: 'Docker build failure'
          ) {
            checkDocker()
          }
        }
      }
      post {
        success  { setStageStats(0) }
        unstable { setStageStats(1) }
        failure  { setStageStats(2) }
      }
    }
  }
  post {
    always {
      node('built-in') { script { generateReports() } }
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
      node('built-in') { script { sendTelegramMessage('fixed') } }
    }
    unstable {
      node('built-in') { script { sendTelegramMessage('unstable') } }
    }
    failure {
      node('built-in') { script { sendTelegramMessage('failure') } }
    }
  }
}

// Stages

void initializeWindows(String platform) {
  echo "PLATFORM=" + platform + "\nNODE_NAME=" + env.NODE_NAME

  if (params.wipe)
    deleteDir()
  else if (params.clean && params.desktop)
    dir ('desktop-apps') { deleteDir() }

  ArrayList varRepos = getVarRepos(env.BRANCH_NAME, platform, branding.repo)
  checkoutRepos(varRepos)

  if (platform == "windows_x64") {
    if (params.core || params.builder || params.server_ce) {
      buildArtifacts(platform, "opensource")
      buildPackages(platform, "opensource")
    }
    if (params.desktop || params.server_ee || params.server_de) {
      buildArtifacts(platform, "commercial")
      buildPackages(platform, "commercial")
    }
  }

  if (platform == "windows_x86") {
    if (params.core || params.builder) {
      buildArtifacts(platform, "opensource")
      buildPackages(platform, "opensource")
    }
    if (params.desktop) {
      buildArtifacts(platform, "commercial")
      buildPackages(platform, "commercial")
    }
  }

  if (platform in ["windows_x64_xp", "windows_x86_xp"]) {
    if (params.desktop) {
      buildArtifacts(platform, "commercial")
      buildPackages(platform, "commercial")
    }
  }
}

void initializeDarwin(String platform) {
  echo "PLATFORM=" + platform + "\nNODE_NAME=" + env.NODE_NAME

  if (params.wipe)
    deleteDir()
  else if (params.clean && params.desktop)
    dir ('desktop-apps') { deleteDir() }

  ArrayList varRepos = getVarRepos(env.BRANCH_NAME, platform, branding.repo)
  checkoutRepos(varRepos)

  if (platform in ["darwin_x86_64", "darwin_arm64"]) {
    if (params.core || params.builder) {
      buildArtifacts(platform, "opensource")
      buildPackages(platform, "opensource")
    }
  }

  if (params.desktop && env.BRANCH_NAME != "develop") {
    buildArtifacts(platform, "commercial")
    buildPackages(platform, "commercial")
  }
}

void initializeLinux(String platform) {
  echo "PLATFORM=" + platform + "\nNODE_NAME=" + env.NODE_NAME

  if (params.wipe)
    deleteDir()
  else if (params.clean && params.desktop)
    dir ('desktop-apps') { deleteDir() }

  ArrayList constRepos = getConstRepos(env.BRANCH_NAME)
  ArrayList varRepos = getVarRepos(env.BRANCH_NAME, platform, branding.repo)
  ArrayList allRepos = constRepos.plus(varRepos)
  checkoutRepos(varRepos)

  // if (platform == "linux_x86_64") {
  // }
  if (platform == "linux_x86_64_u14") {
    if (params.core || params.builder || params.server_ce) {
      buildArtifacts(platform)
      // if (params.core)      buildCore(platform)
      if (params.builder)   buildBuilder(platform)
      if (params.server_ce) buildServer(platform)
    }
    if (params.desktop || params.server_ee || params.server_de) {
      buildArtifacts(platform, "commercial")
      if (params.desktop)   buildDesktop(platform)
      if (params.server_ee) buildServer(platform, "enterprise")
      if (params.server_de) buildServer(platform, "developer")
    }
  }
  if (platform in ["linux_x86_64", "linux_x86_64_u16"]) {
    if (params.core || params.builder || params.server_ce) {
      buildArtifacts(platform, "opensource")
      buildPackages(platform, "opensource")
    }
    if (params.desktop || params.server_ee || params.server_de) {
      buildArtifacts(platform, "commercial")
      buildPackages(platform, "commercial")
    }
    if (params.server_ce || params.server_ee || params.server_de) {
      buildDocker()
      tagRepos(allRepos, gitTag)
    }
    if (params.test) linuxTest()
  }
  if (platform == "linux_aarch64") {
    if (params.core || params.builder || params.server_ce) {
      buildArtifacts(platform, "opensource")
      buildPackages(platform, "opensource")
    }
    if (params.desktop || params.server_ee || params.server_de) {
      buildArtifacts(platform, "commercial")
      buildPackages(platform, "commercial")
    }
  }
}

void initializeAndroid(String platform = "android") {
  echo "PLATFORM=" + platform + "\nNODE_NAME=" + env.NODE_NAME

  if (params.wipe) deleteDir()

  ArrayList varRepos = getVarRepos(env.BRANCH_NAME, platform, null)
  checkoutRepos(varRepos)

  buildArtifacts(platform, "opensource")
  buildPackages(platform, "opensource")
}

////////////////

void checkoutRepo(
  String repo,
  String branch = 'master',
  String dir = repo.minus(~/^.+\//)
) {
  if (dir == null) dir = repo.minus(~/^.+\//)
  int retryCount = 0
  retry(3) {
    if (retryCount > 0) sleep(30)
    checkout([
      $class: 'GitSCM',
      branches: [[name: 'refs/heads/' + branch]],
      doGenerateSubmoduleConfigurations: false,
      extensions: [
        [$class: 'AuthorInChangelog'],
        [$class: 'RelativeTargetDirectory', relativeTargetDir: dir],
        [$class: 'ScmName', name: "${repo}"],
        [$class: 'SubmoduleOption', recursiveSubmodules: true]
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
    it.tag = true
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
    Map repo = [
      owner: "ONLYOFFICE",
      name: line,
      branch: "master",
      dir: line,
      tag: (line != "onlyoffice.github.io")
    ]
    if (branch != 'master') {
      repo.branch = resolveScm(
        source: [
          $class: 'GitSCMSource',
          remote: "git@github.com:${repo.owner}/${repo.name}.git",
          traits: [[$class: 'jenkins.plugins.git.traits.BranchDiscoveryTrait']]
        ],
        targets: [branch, 'master']
      ).branches[0].name
    }

    repos.add(repo)
  }

  return repos
}

void checkoutRepos(ArrayList repos) {
  echo repos.collect({"${it.owner}/${it.name} (${it.branch})"}).join("\n")
  repos.each {
    checkoutRepo(it.owner + "/" + it.name, it.branch, it.dir)
  }
}

void tagRepos(ArrayList repos, String tag) {
  repos.findAll { it.tag }.each {
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
  Boolean pCore = platform in ["win_64", "win_32", "mac_64", "mac_arm64", "linux_64", "linux_arm64"]
  Boolean pDesktop = platform != "linux_arm64"
  Boolean pBuilder = platform in ["win_64", "win_32", "mac_64", "mac_arm64", "linux_64", "linux_arm64"]
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

void buildPackages(String platform, String license = 'opensource') {
  Boolean isOpenSource = license == "opensource"
  Boolean isCommercial = license == "commercial"
  Boolean pCore = platform in ["windows_x64", "windows_x86", "darwin_x86_64", "darwin_arm64", "linux_x86_64"]
  Boolean pDesktop = !(platform in ["linux_aarch64", "android"])
  Boolean pBuilder = platform in ["windows_x64", "linux_x86_64", "linux_aarch64"]
  Boolean pServer = platform in ["windows_x64", "linux_x86_64", "linux_aarch64"]
  Boolean pMobile = platform == "android"
  ArrayList targets = []
  if (params.core && isOpenSource && pCore)        targets.add("core")
  if (params.desktop && isCommercial && pDesktop)  targets.add("desktop")
  if (params.builder && isOpenSource && pBuilder)  targets.add("builder")
  if (params.server_ce && isOpenSource && pServer) targets.add("server-community")
  if (params.server_ee && isCommercial && pServer) targets.add("server-enterprise")
  if (params.server_de && isCommercial && pServer) targets.add("server-developer")
  if (params.mobile && isOpenSource && pMobile)    targets.add("mobile")
  targets.add("clean")
  targets.add("deploy")
  if (params.signing)                              targets.add("sign")

  String args = " --platform ${platform}" \
              + " --targets ${targets.join(' ')}" \
              + " --version ${env.PRODUCT_VERSION}" \
              + " --build ${env.BUILD_NUMBER}"
  if (!branding.onlyoffice)
    args += " --branding ${branding.repo}"

  if (platforms[platform].isUnix)
    sh "cd build_tools && ./make_package.py ${args}"
  else
    bat "cd build_tools && make_package.py ${args}"

  if (fileExists('deploy.json')) {
    def platformDeployData = readJSON(file: 'deploy.json')
    println platformDeployData
    deployData += platformDeployData
  }
  // if (params.core && isOpenSource && pCore)        deployCore(platform)
  // if (params.desktop && isCommercial && pDesktop)  deployDesktop(platform)
  // if (params.builder && isOpenSource && pBuilder)  deployBuilder(platform)
  // if (params.server_ce && isOpenSource && pServer) deployServer(platform, "community")
  // if (params.server_ee && isCommercial && pServer) deployServer(platform, "enterprise")
  // if (params.server_de && isCommercial && pServer) deployServer(platform, "developer")
  // if (params.mobile && isOpenSource && pMobile)    deployMobile(env.BRANCH_NAME)
}

void buildDocker() {
  sh """
    gh workflow run 4testing-build.yml \
      --repo ONLYOFFICE/Docker-DocumentServer \
      --ref \$BRANCH_NAME \
      -f build=\$BUILD_NUMBER \
      -f amd64=${params.linux_x86_64} \
      -f arm64=${params.linux_aarch64} \
      -f community=${params.server_ce} \
      -f enterprise=${params.server_ee} \
      -f developer=${params.server_de}
  """
}

void checkDocker() {
  sh """
    REPO=ONLYOFFICE/Docker-DocumentServer
    sleep 5
    RUN_ID=\$(gh run list --repo \$REPO --workflow 4testing-build.yml \
      --branch \$BRANCH_NAME --json databaseId --jq '.[0].databaseId')
    gh --repo \$REPO run watch \$RUN_ID --interval 15 > /dev/null
    gh --repo \$REPO run view \$RUN_ID --verbose --exit-status
  """
}

// Deploy Packages

/*
void deployCore(String platform) {
  String branch = env.BRANCH_NAME
  String version = env.PRODUCT_VERSION
  String build = env.BUILD_NUMBER
  LinkedHashMap path = [
    windows_x64:      [os: "windows", version: "${version}.${build}", arch: "x64"],
    windows_x86:      [os: "windows", version: "${version}.${build}", arch: "x86"],
    darwin_x86_64:    [os: "mac",     version: "${version}-${build}", arch: "x64"],
    linux_x86_64:     [os: "linux",   version: "${version}-${build}", arch: "x64"],
    linux_x86_64_u14: [os: "linux",   version: "${version}-${build}", arch: "x64"],
    linux_x86_64_u16: [os: "linux",   version: "${version}-${build}", arch: "x64"]
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

void deployDesktop(String platform) {
  String version = "${env.PRODUCT_VERSION}-${env.BUILD_NUMBER}"

  if (platform ==~ /^windows../) {
    uploadFiles("desktop", platform, [
        [section: "Installer",  glob: "*.exe", dest: "/"],
        [section: "Installer",  glob: "*.msi", dest: "/"],
        [section: "Portable",   glob: "*.zip", dest: "/"],
        [section: "Portable",   glob: "portable/*.exe", dest: "/"],
        [section: "WinSparkle",
         glob: "update/*.exe,update/*.xml,update/*.html", dest: "/"],
      ],
      "desktop-apps/win-linux/package/windows",
      "${s3prefix}/windows/${version}/desktop")

  } else if (platform ==~ /^macos../) {

    String suffix
    ArrayList targets = ['clean']
    if (platform == "darwin_x86_64_v8") {
      suffix = "v8"
    } else if (platform == "darwin_x86_64") {
      suffix = "x86_64"
    } else if (platform == "darwin_arm64") {
      suffix = "arm"
    }

    sh """#!/bin/bash -e
      plistbuddy=/usr/libexec/PlistBuddy
      url=http://download.onlyoffice.com/install/desktop/editors/mac/${suffix}/onlyoffice.xml
      appcast=\$(curl -s \$url 2> /dev/null)
      path=desktop-apps/macos/ONLYOFFICE/Resources/ONLYOFFICE-${suffix}/Info.plist
      echo -n "RELEASE_MACOS_VERSION="
      echo \$appcast | xmllint --xpath "/rss/channel/item[1]/enclosure/@*[name()='sparkle:shortVersionString']" - | cut -f 2 -d \\"
      echo -n "RELEASE_MACOS_BUILD="
      echo \$appcast | xmllint --xpath "/rss/channel/item[1]/enclosure/@*[name()='sparkle:version']" - | cut -f 2 -d \\"
      echo -n "CURRENT_MACOS_VERSION="
      \$plistbuddy -c 'print :CFBundleShortVersionString' \$path
      echo -n "CURRENT_MACOS_BUILD="
      \$plistbuddy -c 'print :CFBundleVersion' \$path
    """
    uploadFiles("desktop", platform, [
        [section: "Disk Image", glob: "*.dmg", dest: "/"],
        [section: "Archive", glob: "${branding.company}-${suffix}-*.zip", dest: "/"],
        [section: "Sparkle", glob: "update/*.delta,update/*.xml,update/*.html", dest: "/"],
      ], "desktop-apps/macos/build", "${s3prefix}/macos/${version}/${suffix}")
    sh """#!/bin/bash -e
      cd desktop-apps/macos/build/update
      md5 *.zip *.delta > md5sums.txt
      shasum -a 256 *.zip *.delta > sha256sums.txt
    """
    uploadFiles("desktop", platform, [
        [section: "Checksums", glob: "*.txt", dest: "/"],
      ], "desktop-apps/macos/build/update", "${s3prefix}/macos/${version}/${suffix}")

  } else if (platform ==~ /^linux../) {

    if (!branding.onlyoffice)
      makeargs += " -e BRANDING_DIR=../../../../${branding.repo}/desktop-apps/win-linux/package/linux"
    if (platform == "linux_aarch64")
      makeargs += " -e UNAME_M=aarch64"
    sh "cd desktop-apps/win-linux/package/linux && \
      make clean && \
      make packages ${makeargs}"
    String distPrefix = ""
    if (platform == "linux_x86_64_u14") distPrefix = "/ubuntu14"
    uploadFiles("desktop", platform, [
        [section: "Ubuntu",   glob: "deb/*.deb",        dest: "/ubuntu/"  ],
        [section: "CentOS",   glob: "rpm/*./*.rpm",     dest: "/centos/"  ],
        [section: "AltLinux", glob: "apt-rpm/*./*.rpm", dest: "/altlinux/"],
        [section: "Rosa",     glob: "urpmi/*./*.rpm",   dest: "/rosa/"    ],
        [section: "SUSE Linux", glob: "suse-rpm/*./*.rpm", dest: "/suse/" ],
        [section: "Portable", glob: "tar/*.tar.*",      dest: "/linux/"   ],
        // [section: "AstraLinux Signed",
        //  glob: "deb-astra/*.deb", dest: "/astralinux/"]
      ], "desktop-apps/win-linux/package/linux", s3prefix + distPrefix)
    uploadFiles("desktop", platform, [
        [section: "AppImage", glob: "out/*.AppImage", dest: "/appimage/"]
      ], "appimage-desktopeditors", s3prefix)
  }
}

void deployBuilder(String platform) {
  String version = "${env.PRODUCT_VERSION}-${env.BUILD_NUMBER}"
  String makeargs = ""

  if (platform ==~ /^windows../) {

    ArrayList targets = ['clean']
    if (platform == 'windows_x64') targets += ['innosetup-x64', 'portable-x64']
    if (platform == 'windows_x86') targets += ['innosetup-x86', 'portable-x86']
    if (params.signing) targets += ['sign']
    buildPackages("builder", platform, targets)
    uploadFiles("builder", platform, [
        [section: "Installer", glob: "exe/*.exe", dest: "/"],
        [section: "Portable",  glob: "zip/*.zip", dest: "/"]
      ], "document-builder-package", "${s3prefix}/windows/${version}/builder")

  } else if (platform ==~ /^macos../) {

    ArrayList targets = ['clean', 'builder']
    String suffix
    if (platform == "darwin_x86_64") {
      suffix = "x86_64"
    } else if (platform == "darwin_arm64") {
      suffix = "arm"
    }
    sh """#!/bin/bash -e
      rm -rfv document-builder-package/*.xz
      cd build_tools/out/${platforms[platform].build}/onlyoffice/documentbuilder
      tar --xz -cvf ../../../../../document-builder-package/onlyoffice-documentbuilder-${suffix}-${version}.tar.xz *
    """
    // buildPackages("builder", platform, targets)
    uploadFiles("builder", platform, [
        [section: "Portable",  glob: "*.xz", dest: "/"]
      ], "document-builder-package", "${s3prefix}/macos/${version}/${suffix}")

  } else if (platform ==~ /^linux../) {

    if (platform == "linux_aarch64")
      makeargs += " -e UNAME_M=aarch64"
    if (!branding.onlyoffice)
      makeargs += " -e BRANDING_DIR=../${branding.repo}/document-builder-package"
    sh "cd document-builder-package && \
      make clean && \
      make packages ${makeargs}"
    String distPrefix = ""
    if (platform == "linux_x86_64_u14") distPrefix = "/ubuntu14"
    uploadFiles("builder", platform, [
        [section: "Ubuntu",   glob: "deb/*.deb",    dest: "/ubuntu/"],
        [section: "CentOS",   glob: "rpm/*./*.rpm", dest: "/centos/"],
        [section: "Portable", glob: "tar/*.tar.gz", dest: "/linux/" ]
      ], "document-builder-package", s3prefix + distPrefix)
    uploadFiles("builder", platform, [
        [section: "AppImage", glob: "out/*.AppImage", dest: "/appimage/"]
      ], "appimage-documentbuilder", s3prefix + distPrefix)
  }
}

void deployServer(String platform, String edition='community') {
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

  if (platform ==~ /^windows../) {

    makeargs = "-e PRODUCT_NAME=${productName}"
    if (!branding.onlyoffice)
      makeargs += " -e BRANDING_DIR=../${branding.repo}/document-server-package"
    bat "cd document-server-package && \
      make clean && \
      make packages ${makeargs}"
    uploadFiles(product, platform, [
        [section: "Installer", glob: "exe/*.exe", dest: "/"  ]
      ], "document-server-package", "${s3prefix}/windows/${version}/server")

  } else if (platform ==~ /^linux../) {

    makeargs = "-e PRODUCT_NAME=${productName.toLowerCase()}"
    if (platform == "linux_aarch64")
      makeargs += " -e UNAME_M=aarch64"
    if (!branding.onlyoffice)
      makeargs += " -e BRANDING_DIR=../${branding.repo}/document-server-package"
    sh "cd document-server-package && \
      make clean && \
      make packages ${makeargs}"
    String distPrefix = ""
    if (platform == "linux_x86_64_u14") distPrefix = "/ubuntu14"
    uploadFiles(product, platform, [
        [section: "Ubuntu",   glob: "deb/*.deb",        dest: "/ubuntu/"  ],
        [section: "CentOS",   glob: "rpm/*./*.rpm",     dest: "/centos/"  ],
        [section: "AltLinux", glob: "apt-rpm/*./*.rpm", dest: "/altlinux/"],
        [section: "Portable", glob: "*.tar.gz",         dest: "/linux/"   ]
      ], "document-server-package", s3prefix + distPrefix)
  }
}

void deployMobile(String branch = 'master', String config = 'release') {
  String version = "${env.PRODUCT_VERSION}-${env.BUILD_NUMBER}"

  sh "rm -rfv *.zip && \
    cd build_tools/out && \
    zip -r ../../android-libs-${version}.zip ./android* ./js"
  uploadFiles("mobile", "android", [
      [section: "Libs", glob: "*.zip", dest: "/android/"],
    ], "", s3prefix)
}

// Upload

void uploadFiles(
  String product,
  String platform,
  ArrayList items,
  String srcPrefix = '',
  String destPrefix = ''
) {
  String srcPath, uploadCmd = ""
  ArrayList localDeployData = []

  dir(srcPrefix) {
    items.each { item ->
      findFiles(glob: item.glob).each { file ->
        srcPath = "${destPrefix}${item.dest}"
        localDeployData.add([
          product: product,
          platform: platforms[platform].title,
          type: item.type,
          remote: srcPath + file.name,
          file: file.name,
          size: file.length
          // md5: cmdMd5sum(file.path)
          // sha256: cmdSha256sum(it.path)
        ])
        uploadCmd += "aws s3 cp --acl public-read --no-progress " \
          + "${file.path} s3://${srcPath}; "
      }
    }
    if (platforms[platform].isUnix) sh uploadCmd else powershell uploadCmd
  }

  deployData.addAll(localDeployData)
}
*/

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
  Map deploy = deployData.groupBy { it.product }
  println deploy

  Boolean core = deploy.core != null
  Boolean desktop = deploy.desktop != null
  Boolean builder = deploy.builder != null
  Boolean server_ce = deploy.server_community != null
  Boolean server_ee = deploy.server_enterprise != null
  Boolean server_de = deploy.server_developer != null
  Boolean mobile = deploy.mobile != null

  deleteDir()
  if (core)
    publishReport("Core", ["core.html": deploy.core])
  if (desktop)
    publishReport("DesktopEditors", ["desktop.html": deploy.desktop])
  if (builder)
    publishReport("DocumentBuilder", ["builder.html": deploy.builder])
  if (server_ce || server_ee || server_de) {
    Map serverReports = [:]
    if (server_ce) serverReports."server_community.html" = deploy.server_community
    if (server_ee) serverReports."server_enterprise.html" = deploy.server_enterprise
    if (server_de) serverReports."server_developer.html" = deploy.server_developer
    publishReport("DocumentServer", serverReports)
  }
  if (mobile)
    publishReport("Mobile", ["mobile.html": deploy.mobile])
}

void publishReport(String title, Map files) {
  files.each {
    writeFile file: it.key, text: getHtml(it.value)
    try {
      withCredentials([
        string(credentialsId: 'aws-access-key-id', variable: 'AWS_ACCESS_KEY_ID'),
        string(credentialsId: 'aws-secret-access-key', variable: 'AWS_SECRET_ACCESS_KEY')
      ]) {
        sh "aws s3 cp --acl public-read --no-progress ${it.key} \
          s3://repo-doc-onlyoffice-com/${branding.company_lc}/reports/${env.BRANCH_NAME}/${env.BUILD_NUMBER}/"
      }
      echo "https://s3.eu-west-1.amazonaws.com/repo-doc-onlyoffice-com/${branding.company_lc}/reports/${env.BRANCH_NAME}/${env.BUILD_NUMBER}/${it.key}"
    } catch(Exception e) {
      echo "Caught: ${e}"
    }
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
    + "\n<link rel=\"stylesheet\" href=\"https://unpkg.com/@primer/css@20.4.1/dist/primer.css\">" \
    + "\n</head>\n<body><div class=\"container-lg px-3 my-5 markdown-body\">"
  data.groupBy { it.platform }.sort().each { platform, types ->
    text += "\n<h2>${platform}</h2>\n<dl>"
    types.groupBy { it.type }.each { type, files ->
      text += "\n<dt>${type}</dt>\n<dd>"
      files.each {
        title = it.key.minus(~/^.+\//)
        url = "https://s3.${it.region}.amazonaws.com/${it.bucket}/${it.key}"
        text += "\n<a href=\"${url}\">${title}</a> (${size(it.size)}B)<br>"
      }
      text += "\n</dd>"
    }
    text += "\n</dl>"
  }
  text += "\n</div>\n</body>\n</html>"

  return text
}

// Notifications

void setStageStats(int status, String stageName = env.STAGE_NAME) {
  stageStats[stageName] = status
}

void sendTelegramMessage(
  String jobStatus,
  String chatId = '-1001773122025',
  Boolean markdown = true
) {
  String text = "Build [" + currentBuild.fullDisplayName \
      + "](" + currentBuild.absoluteUrl + ") " + jobStatus
  ArrayList icons = ["🟢", "🟡", "🔴"]
  stageStats.sort().each { stage, code ->
    text += "\n" + icons[code] + " " + stage.replaceAll('_','\\\\_')
  }

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
