# K3S

[K3s](https://k3s.io/) - это полностью совместимый c Kubernetes облегчённый дистрибутив.

* Сертифицирован [CNCF](https://www.cncf.io/)(Сloud Native Computing Foundation). Это означает, что он прошел 
тесты на соответствие, используемые CNCF для сертификации дистрибутивов Kubernetes.
* Является [проектом песочницы](https://www.cncf.io/projects/k3s/) CNCF.

[Документация](https://rancher.com/docs/k3s/latest/en/).


## Подготовка стенда:

1.Настроить hostname на всех вм 
2.Прописать статические IP для вм 
3.Прописать hosts файл
4.Выдать права пользователю 
5.Установить на все вм необходимые зависимости



## Firewall

```shell
firewall-cmd --permanent --zone=public --add-port=6443/tcp
firewall-cmd --permanent --zone=public --add-port=8472/udp
firewall-cmd --permanent --zone=public --add-port=51820/tcp
firewall-cmd --permanent --zone=public --add-port=51821/tcp
firewall-cmd --permanent --zone=public --add-port=10250/tcp
firewall-cmd --permanent --zone=public --add-port=2379-2380/tcp
firewall-cmd --complete-reload
```

## Установка

### Первый мастер.

```shell
curl -sfL https://get.k3s.io | K3S_TOKEN='l%TH]c4VvCT<Xj{' sh -s - server --cluster-init --write-kubeconfig-mode 644
```

Можно с дополнительными параметрами:

```shell
curl -sfL https://get.k3s.io | K3S_TOKEN='l%TH]c4VvCT<Xj{' sh -s - server --cluster-init \
     --flannel-iface "ens33" \
     --cluster-cidr "10.223.0.0/18"  --service-cidr "10.224.0.0/18" --cluster-dns "10.223.0.10" \
     --default-local-storage-path "/var/k3s/storage" \
     --data-dir "/var/k3s/data"
```

```shell
kubectl get nodes
```

### Дополнительные мастера.

Устанавливаем остальные мастера. Общее количество мастеров должно быть нечётным.

```shell
curl -sfL https://get.k3s.io | K3S_TOKEN='l%TH]c4VvCT<Xj{' sh -s - server --server https://kubemaster:6443
```

Если с дополнительными параметрами:

```shell
curl -sfL https://get.k3s.io | K3S_TOKEN='l%TH]c4VvCT<Xj{' sh -s - server --server https://kubemaster:6443 \
     --flannel-iface "ens33" \
     --cluster-cidr "10.223.0.0/18"  --service-cidr "10.224.0.0/18" --cluster-dns "10.223.0.10" \
     --default-local-storage-path "/var/k3s/storage" \
     --data-dir "/var/k3s/data"
```

### Worker ноды.

На мастер ноде получаем токен.

```shell
sudo cat /var/lib/rancher/k3s/server/token
```

```shell
cat /var/k3s/data/server/token
```

Подставляем его в переменную и запускаем установку.

```shell
curl -sfL https://get.k3s.io | K3S_URL="https://kubemaster:6443" \
      K3S_TOKEN='K1065997b4a23140b336dd0f3d2b07813536812eaa1b0c4e07d5a7e7df481963346::server:l%TH]c4VvCT<Xj{' \
      sh -
```

Если необходимо использовать дополнительные параметры, определяйте их в переменной ```INSTALL_K3S_EXEC```:

```shell
curl -sfL https://get.k3s.io | K3S_URL="https://k3sm1.kryukov.local:6443" \
      K3S_TOKEN='K1065997b4a23140b336dd0f3d2b07813536812eaa1b0c4e07d5a7e7df481963346::server:l%TH]c4VvCT<Xj{' \
      INSTALL_K3S_EXEC="--flannel-iface ens33 --data-dir /var/k3s/data" \
      sh -
```

## Видео:

* Youtube: https://youtu.be/p_8mXjZxFb0
* Telegram: https://t.me/arturkryukov/60
* VK: https://vk.com/video7111833_456239202