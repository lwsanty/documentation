pipeline {
  agent {
    kubernetes {
      label 'sdk-drivers-updater'
      defaultContainer 'sdk-drivers-updater'
      yaml """
spec:
  nodeSelector:
    srcd.host/type: jenkins-worker
  affinity:
    podAntiAffinity:
      requiredDuringSchedulingIgnoredDuringExecution:
      - labelSelector:
          matchExpressions:
          - key: jenkins
            operator: In
            values:
            - slave
        topologyKey: kubernetes.io/hostname
  containers:
  - name: sdk-drivers-updater
    image: bblfsh/performance:latest
    imagePullPolicy: Always
    securityContext:
      privileged: true
    command:
    - dockerd
    tty: true
"""
    }
  }
  triggers {
    GenericTrigger(
      genericVariables: [
        [key: 'target', value: '$.target']
      ],
      token: 'update',
      causeString: 'Triggered on $target',

      printContributedVariables: true,
      printPostContent: true,

      regexpFilterText: '$target',
      regexpFilterExpression: 'master'
    )
  }
  stages {
    stage('Run updater') {
      when { branch 'auto-update-languages' }
      steps {
        withCredentials([usernamePassword(credentialsId: 'jtmp', passwordVariable: 'token', usernameVariable: 'user')]) {
          sh 'GITHUB_TOKEN=${token} make update'
        }
      }
    }
  }
}