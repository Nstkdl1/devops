Данный файл *depmanager.yaml* предназначен для Google Deployment Manager
Он выполняет такие действия
1) Указывает Goggle DM поднять виртуальную машину типа e2-micro для проекта spheric-base-332521 в зоне asia-northeast1-b с одним persistent диском разсеров 8гб и с названием "task-6", а также прикрепление к ней тэга 'http'. Какже в ниже представленном блоке указывается ОС Debian 9
За это отвечает данные строки
    ```yaml
    type: compute.v1.instance
      name: task-6
      properties:
        zone: asia-northeast1-b
        machineType: https://www.googleapis.com/compute/v1/projects/spheric-base-332521/zones/asia-northeast1-b/machineTypes/e2-micro
        disks:
        - deviceName: boot
          type: PERSISTENT
          boot: true
          autoDelete: true
          sizeGb: 8
          initializeParams:
            sourceImage: https://www.googleapis.com/compute/v1/projects/debian-cloud/global/images/family/debian-9
        tags:
          items: ["http"]
    ```

2) Добавление сетевого интерфейса к виртуальной машине
    ```yaml
    networkInterfaces:
    - network: https://www.googleapis.com/compute/v1/projects/spheric-base-332521/global/networks/default
      accessConfigs:
      - type: ONE_TO_ONE_NAT
    ```
3) Обновление кэша пакетного менеджера и последующая установка nginx, а также запись текста в стандарный index файл для его отображения
    ```yaml
    metadata:
      items:
        - key: startup-script
          value: |
            #!/bin/bash
            apt update
            apt install -y nginx
            echo 'Hello world from Crash course DevOps' > /var/www/html/index.html
            service nginx reload
    
    ```
    
4) Открытие входящего траффика для виртуальной машины (тэг html, упомянут выше)
    ```yaml
    - name: default-allow-http
      type: compute.v1.firewall
      properties:
        targetTags: ["http"]
        sourceRanges: ["0.0.0.0/0"]
        allowed:
          - IPProtocol: TCP
            ports: ["80"]    
    ```