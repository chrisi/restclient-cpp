pipeline {
  agent  { label 'cloud-arm64' }
  environment {
    GTI_CMAKE_REPO = '/tmp'
  }
  stages {
    stage('Install curl') {
      steps {
        sh("users")
        sh("/opt/vcpkg install curl")
      }
    }
    stage('Build Project') {
      steps {
        sh("mkdir -p build")
        dir("build") {
          sh("cmake -DCMAKE_TOOLCHAIN_FILE=/opt/vcpkg/scripts/buildsystems/vcpkg.cmake -DCMAKE_BUILD_TYPE=Release ..")
          sh("cmake --build .")
        }
      }
    }
    stage('Install Project') {
      steps {
        dir("build") {
          sh("make install")
        }
      }
    }
    stage('Package Project') {
      steps {
        dir("build") {
          sh("cpack")
        }
      }
    }
    stage('Upload Package') {
      steps {
        dir("build") {
          sh('curl -u "deb:latrop0815" -H "Content-Type: multipart/form-data" --data-binary "@./rest-client.deb" "https://nexus.gtidev.net/repository/gtideb/"')
        }
      }
    }
  }
}
