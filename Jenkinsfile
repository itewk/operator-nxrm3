/*
 * Copyright (c) 2016-present Sonatype, Inc. All rights reserved.
 * Includes the third-party code listed at http://links.sonatype.com/products/nexus/attributions.
 * "Sonatype" is a trademark of Sonatype, Inc.
 */
@Library(['private-pipeline-library', 'jenkins-shared', 'int-jenkins-shared']) _
import com.sonatype.jenkins.pipeline.GitHub

properties([
  parameters([
    booleanParam(defaultValue: false, description: 'Force Red Hat Certified Build for a non-master branch', name: 'force_red_hat_build'),
    booleanParam(defaultValue: false, description: 'Skip Red Hat Certified Build', name: 'skip_red_hat_build'),
    string(defaultValue: '', description: 'Override automatic version assignment', name: 'version'),
    string(defaultValue: '1', description: 'Serial number for bundle image', name: 'bundle_number'),
  ])
])

def version
def isMaster
final organization = 'sonatype'

dockerizedBuildPipeline(
  buildImageId: 'docker-all.repo.sonatype.com/operator-framework/upstream-registry-builder',
  prepare: {
    githubStatusUpdate('pending')
  },
  setVersion: {
    version = params.version ?: readVersion()
  },
  buildAndTest: {
    withCredentials([
      string(credentialsId: 'operator-bundle-nxrm-rh-project-id', variable: 'PROJECT_ID'),
      string(credentialsId: 'rh-docker-registry-key', variable: 'KEY')]) {
      runSafely "scripts/bundle.sh ${params.bundle_number} ${PROJECT_ID} ${KEY}"
    }
  },
  skipVulnerabilityScan: true,
  deployCondition: {
    true
  },
  deploy: {
    withCredentials([
      string(credentialsId: 'operator-bundle-nxrm-rh-project-id', variable: 'PROJECT_ID'),
      string(credentialsId: 'rh-docker-registry-key', variable: 'KEY'),
      string(credentialsId: 'rh-docker-registry-key', variable: 'JENKINS_DOCKER_USERNAME'),
      string(credentialsId: 'rh-docker-registry-key', variable: 'JENKINS_DOCKER_PASSWORD'),
    ]) {
      runSafely "scripts/deploy_bundle.sh ${params.bundle_number} ${PROJECT_ID} ${KEY}"
    }
  },
  onSuccess: {
    buildNotifications(currentBuild, env, 'master')
  },
  onFailure: {
    buildNotifications(currentBuild, env, 'master')
  },
)

def readVersion() {
  def content = readFile 'build/Dockerfile'
  for (line in content.split('\n')) {
    if (line.contains('version=')) {
      return line.split('=')[1].replaceAll(/[^\d.-]+/, '').trim()
    }
  }
  error 'Could not determine version from build/Dockerfile.'
}
