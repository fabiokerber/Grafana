# Grafana

Pré requisito:

|Tool    |Link|
|-------------|-----------|
|`Vagrant`| https://releases.hashicorp.com/vagrant/2.2.19/vagrant_2.2.19_x86_64.msi
|`VirtualBox`| https://download.virtualbox.org/virtualbox/6.1.30/VirtualBox-6.1.30-148432-Win.exe

# Geral
Dashboard é uma coleção de painéis.<br>
Painel é um mostrador de métricas.<br>

# Instalação inicial Grafana
```
> cd install
!! edit .env (GRAFANA_IP='192.168.0.245')
> vagrant up (irá subir a VM do Grafana e centos_srv03)
> vagrant ssh gitlab_srv -c 'sudo cat /etc/gitlab/initial_root_password | grep Password:' (anotar!)

http://192.168.0.245:3000
    admin | (admin)
```

# Primeira Dashboard
Adicionar painel e Query.<br>
Query "Random Walk", são números gerados de forma aleatória apenas para exemplo de gráfico mesmo.<br> 
<kbd>
    <img src="https://github.com/fabiokerber/Grafana/blob/main/img/180220221516.png">
</kbd>
<br />
<br />
<kbd>
    <img src="https://github.com/fabiokerber/Grafana/blob/main/img/180220221516.png">
</kbd>
<br />
<br />
<kbd>
    <img src="https://github.com/fabiokerber/Grafana/blob/main/img/180220221523.png">
</kbd>
<br />
<br />