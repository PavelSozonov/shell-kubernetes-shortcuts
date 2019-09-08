# zsh-bash-kubernetes-short-aliases

Shorcuts for kubernetes with several contexts support.

## Usage:
| Shortcut | Short example                            | Full example                                      | Comment                                   |
|----------|------------------------------------------|---------------------------------------------------|-------------------------------------------|
| k        | k get pods                               | kubectl get pods                                  | Any command with default context          |
| kd       | kd get pods                              | kubectl --context dev get pods                    | Any command with "dev" context            |
| kt       | kt get pods                              | kubectl --context test get pods                   | Any command with "test" context           |
| kde      | kde <unique-part-of-pod-name>            | kubectl --conetxt dev exec -it <pod-name> -- sh   | Open shell inside a pod in "dev" context  |
| kte      | kte <unique-part-of-pod-name>            | kubectl --context test exec -it <pod-name> -- sh  | Open shell inside a pod in "test" context |
| kdl      | kle <unique-part-of-pod-name> [--follow] | kubectl --context dev logs <pod-name> [--follow]  | Show logs for a pod in "dev" cotext       |
| ktl      | ktl <unique-part-of-pod-name> [--follow] | kubectl --context test logs <pod-name> [--follow] | Show logs for a pod in "test" context     |


## Configure:

`Bash`: append `~/.bash_profile` or `~/.bashrc`

`Zsh`: append `~/.zshrc`

```sh
kdl() {
        klog "dev" $1 ${@:2}
}

ktl() {
        klog "test" $1 ${@:2}
}

kde() {
        kexec "dev" $1
}

kte() {
        kexec "test" $1
}

# Match pod names with $2 arg, and return the last of them
#
# Args:
# $1 - context name
# $2 - pod name matching pattern (substring)
get_pod() {
        echo `kubectl --context $1 get pods | grep $2 | tail -1 | awk '{ print $1 }'`
}

# Show logs for pod which matches with $2 substring
#
# Agrs:
# $1 - context name
# $2 - pod name matching pattern (substring)
# ${@:3} - additional arguments, e.g. "--follow"
klog() {
        pod=`get_pod $1 $2`
        echo "Pod name: ${pod}"
        kubectl --context $1 logs ${pod} ${@:3}
}

# Run sh inside pod which matches with $2 substring
# 
# Args:
# $1 - context name
# $2 - pod name matching pattern (substring)
kexec() {
        pod=`get_pod $1 $2`
        echo "Pod name: ${pod}"
        kubectl --context $1 exec -it ${pod} -- sh
}

alias k=kubectl
alias kd="kubectl --context dev"
alias kt="kubectl --context test"
```

Apply changes: 

```zsh
source ~/.zshrc
```

```bash
source ~/.bash_profile # or source ~/.bashrc
```
