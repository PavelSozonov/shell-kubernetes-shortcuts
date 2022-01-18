# shell-kubernetes-shortcuts

Shorcuts for kubernetes with several contexts support.

## Usage:
| Shortcut | Short example                                  | Full example                                                                       | Comment                                                                        |
|----------|------------------------------------------------|------------------------------------------------------------------------------------|--------------------------------------------------------------------------------|
| k        | k get pods                                     | kubectl get pods                                                                   | Any command with default context                                               |
| kev      | kev &lt;unique-part-of-pod-name&gt;            | kubectl get event -w --field-selector involvedObject.name=&lt;part-of-pod-name&gt; | Listen events for a pod                                                        |
| ke       | ke &lt;unique-part-of-pod-name&gt;             | kubectl exec -it &lt;pod-name&gt; -- sh                                            | Open shell inside a pod                                                        |
| kl       | ke &lt;unique-part-of-pod-name&gt; [--follow]  | kubectl logs &lt;pod-name&gt; [--follow]                                           | Show logs for a pod                                                            |
| kgp      | kgp &lt;part-of-pod-name&gt; (or without args) | kubectl get pod &#124; grep &lt;part-of-pod-name&gt;                               | Show "get pod" info for pods which matches with $1 substring (or for all pods) |

## Configure:

`Bash`: 

append `~/.bash_profile` or `~/.bashrc`

`Zsh`: 

append `~/.zshrc`

```
kev() {
        kevent $1
}

kl() {
        klog $1 ${@:2}
}

ke() {
        kexec $1
}

kgp() {
        if [ $# -ne 0 ]; then
          kpod $1
        else
          kubectl get pods
        fi
}

# Match pod names with $1 arg, and return the last of them
#
# Args:
# $1 - pod name matching pattern (substring)
get_pod() {
        echo `kubectl get pods | grep $1 | tail -1 | awk '{ print $1 }'`
}

# Show "get pod" info for pods which matches with $1 substring
#
# Agrs:
# $1 - pod name matching pattern (substring)
kpod() {
        kubectl get pod | grep $1
}

# Show logs for pod which matches with $1 substring
#
# Agrs:
# $1 - pod name matching pattern (substring)
# ${@:2} - additional arguments, e.g. "--follow"
klog() {
        pod=`get_pod $1`
        echo "Pod name: ${pod}"
        kubectl logs ${pod} ${@:2}
}

# Run sh inside pod which matches with $1 substring
# 
# Args:
# $1 - pod name matching pattern (substring)
kexec() {
        pod=`get_pod $1`
        echo "Pod name: ${pod}"
        kubectl exec -it ${pod} -- sh
}

# Listen vents for pod which matches with $1 substring
# 
# Args:
# $1 - pod name matching pattern (substring)
kevent() {
        pod=`get_pod $1`
        echo "Pod name: ${pod}"
        kubectl get event -w --field-selector involvedObject.name=${pod}
}

alias k=kubectl
```

Apply changes: 

`Zsh:`
```zsh
source ~/.zshrc
```

`Bash:`
```bash
source ~/.bash_profile # or source ~/.bashrc
```
