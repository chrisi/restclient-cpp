pipeline {
  agent  { label 'cloud-arm64' }
  environment {
    GTI_CMAKE_REPO = '/tmp'
  }
  stages {
    stage('Download Dependencies') {
      steps {
        sh("apt update && apt install -y curl zip unzip tar")
      }
    }
    stage('Init Package-Manager') {
      steps {
        sh("git clone https://github.com/Microsoft/vcpkg.git")
        dir("vcpkg") {
          sh("./bootstrap-vcpkg.sh")
          sh("./vcpkg integrate install")
        }
      }
    }
    stage('Install curl') {
      steps {
        dir("vcpkg") {
          sh("./vcpkg install curl")
        }
      }
    }
    stage('Build Project') {
      steps {
        sh("mkdir -p build")
        dir("build") {
          sh("cmake -DCMAKE_BUILD_TYPE=Release ..")
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
