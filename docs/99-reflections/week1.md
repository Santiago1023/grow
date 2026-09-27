# Week 1 Reflection – NGINX Pod and Deployment

This week, I worked through the Kubernetes fundamentals lab where I deployed a basic NGINX web server using both a Pod and a Deployment. I also learned about security contexts and how to scale workloads.

---

## ✅ What I Learned

This exercise was very interesting because I could know something that it was hidden for me, the fact that to be able to work with kubernetes in a properly way, there are a lot of steup behind the architecture of Kubernetes.
I understand that windows and wsl (linux on windows) are like complete different environments.
Finally, the most important thing was troubleshooting and understand the reasons of why something is wrong, or not work properly, it is very difficult to notice that something happend when incluse you do not see any kind of error, so for example I had one cluster deployed from powershell from windows, and I started this lab inside wsl and create the kind cluster, when I tried to run some commands in the new cluster, it was not working, the cluster were running, but I could not run my workload, after reviewing some documentation, I found that for every cluster it is mandatory to have the correct authentication and authorization methods, so at this moment I know about the ~kube directory, and what is a kubeconfig file.
Also, I could differentiate two kinds of outputs, one related with the lack of the kubeconfig file, and the other one where the cluster is not running.


---

## ❓ What Was Challenging

- For me was challenging differentiate the two environments, wsl and windows, I thougt that it was the same thing, and everything in my own pc could be access by the two environments.
- Be patient, where something not work, but you do not find nothing that seems damaged, with errors or problems.

---

## 🧪 Commands I Practiced

```bash




```

---

## 🔐 Security Improvements I Made

- 
- 

---

## 📝 Questions I Still Have

- 
- 
- 

---

## 📎 Related YAMLs

- `nginx-pod.yaml`
- `nginx-deployment.yaml`

---

## 🚀 Looking Ahead

I’m excited to explore GitOps in Week 2 and automate deployments using FluxCD.

