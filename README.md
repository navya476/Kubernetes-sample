# Kubernetes-sample

Take a t2.medium ubuntu 16.04 instance.

####### Installing Kubernetes #####################
sudo su

apt-get update && apt-get install -y apt-transport-https

curl -s https://packages.cloud.google.com/apt/doc/apt-key.gpg | apt-key add -

cat <<EOF >/etc/apt/sources.list.d/kubernetes.list
deb http://apt.kubernetes.io/ kubernetes-xenial main
EOF

apt-get update

# Install docker if you don't have it already.

apt-get install -y docker-engine

apt-get install -y kubelet kubeadm kubectl kubernetes-cni

kubeadm init --apiserver-advertise-address ##Public IP of Master##

###### Note the join token produced by above command ##########
###### eg., kubeadm join --token dcb2ea.b8b831b261979998 10.0.0.228:6443 ############

##### Enabling the Pod network for pods to communicate with each other ###############

kubectl apply -f https://git.io/weave-kube-1.6

####### to be able to have pods run on master as well as on nodes ##########

kubectl taint nodes --all node-role.kubernetes.io/master-

####### To deploy web application (frontend) ############

# Deploys a java app in a pod

kubectl run java --image=navyateja/java-appku --port=8080

# Creating a service 

##### java.yml is a configuration file for creating a service

kubectl create -f services/java.yml

# verify the app running on http://PublicIP of Master or Node:30095#########

########### To deploy database MySql (backend) ############

# Deploying a MySql db in another pod

kubectl run mysql --image=navyateja/mysql --env='MYSQL_ROOT_PASSWORD=Welcome@1234' --port=3306

# Creating a service on port 30306

##### mysql.yml is a configuration file for creating a service

kubectl create -f services/mysql.yml



########################################################
If there is any error like localhost can't be recognised
then use following commands.
########################################################

sudo cp /etc/kubernetes/admin.conf $HOME/

sudo chown $(id -u):$(id -g) $HOME/admin.conf

export KUBECONFIG=$HOME/admin.conf

