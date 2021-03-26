/*
 * Copyright (c) 2016-present Sonatype, Inc. All rights reserved.
 * Includes the third-party code listed at http://links.sonatype.com/products/nexus/attributions.
 * "Sonatype" is a trademark of Sonatype, Inc.
 */
@Library(['private-pipeline-library', 'jenkins-shared']) _
import com.sonatype.jenkins.pipeline.GitHub
import com.sonatype.jenkins.pipeline.OsTools

properties([
  parameters([
    booleanParam(defaultValue: false, description: 'Force Red Hat Certified Build for a non-master branch', name: 'force_red_hat_build'),
    booleanParam(defaultValue: false, description: 'Skip Red Hat Certified Build', name: 'skip_red_hat_build'),
    string(defaultValue: '', description: 'Override automatic version assignment', name: 'version'),
    string(defaultValue: '1', description: 'Serial number for bundle image', name: 'bundle_number'),
  ])
])

def imageName = 'docker-all.repo.sonatype.com/operator-framework/upstream-registry-builder'

def version, isMaster
def organization = 'sonatype'

dockerizedBuildPipeline(
  prepare: {
    githubStatusUpdate('pending')
  },
  setVersion: {
    version = params.version ?: readVersion()
  },
  buildAndTest: {
  },
  onSuccess: {
    buildNotifications(currentBuild, env, 'master')
  },

  onFailure: {
    buildNotifications(currentBuild, env, 'master')
  }
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
