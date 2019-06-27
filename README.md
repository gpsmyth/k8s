Kubernates - Minikube installation
https://developer.apple.com/library/archive/technotes/tn2459/_index.html
referes to Security & Privacy settings when installing virtualbox

'''
brew cask install virtualbox
brew cask install minikube (also installed kubernetes-cli)
kubectl version
'''

From https://matthewpalmer.net/kubernetes-app-developer/articles/guide-install-kubernetes-mac.html 
'''
minikube start
kubectl api-versions
'''

Upgrade of bash required on mac osx - noted via https://itnext.io/upgrading-bash-on-macos-7138bd1066ba from https://kubernetes.io/docs/tasks/tools/install-kubectl/#install-kubectl-on-macos

Add the following to your ~/.bash_profile:
  [[ -r "/usr/local/etc/profile.d/bash_completion.sh" ]] && . "/usr/local/etc/profile.d/bash_completion.sh"

If you'd like to use existing homebrew v1 completions, add the following before the previous line:
  export BASH_COMPLETION_COMPAT_DIR="/usr/local/etc/bash_completion.d"

From https://github.com/kubernetes/minikube
 kubectl run hello-minikube --image=k8s.gcr.io/echoserver:1.4 --port=8080
kubectl run --generator=deployment/apps.v1 is DEPRECATED and will be removed in a future version. Use kubectl run --generator=run-pod/v1 or kubectl create instead.

