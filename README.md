## Bağlantılar

- [prometheus.yaml Ayar dosyasında olabilecek job tanımlarına dair örnekler](https://github.com/prometheus/prometheus/blob/0a8d28ea932ed18e000c2f091200a46d2b62bac4/config/testdata/conf.good.yml)



## nginx Exporter
Prometheus sunucusu ve metrik üreten bir nginx sunucusu ayakalnıyor.
Nginx sunucusu metrics isimli dosyayı 80 portundan sanki bir uygulamanın metriklerini sunuyor gibi yayımlıyor.
Nginx 80 dahili portunda çalışıyor ve hostun 80 portundan erişilebiliyor.
http://localhost:80/metrics


## PROMETHEUS
Prometheus varsayılan olarak `/metrics` adresine gider ve eğer farklı adrese gitmesi istenirse `metric_path` alanı job içinde tanımlanmalıdır:

```yaml
- job_name: "ornek job"
  metrics_path: /sunucu-adresine/ek-olacak/metrik-adresi
  static_configs:
    targets: ["sunucu-adresi.com", "192.168.1.19", ...]
```

Prometheus konteyneri içeride 8090 portunda çalışırken dışarıda 90 portundan erişilebilir. 
http://localhost:90

## Docker Komutları
Tüm konteynerleri >
Yansıları derlemek için:

```shell
docker-compose -f .\docker-compose.yaml build
```

Çalıştırmak için
```shell
docker-compose -f .\docker-compose.yaml up
```

Durdurmak için:

```shell
docker-compose -f .\docker-compose.yaml down
```
## GRAFANA
Sadece Grafayı ayaklandırmak için:
```shell
 docker run -d \
     -p 3000:3000 \
     --name=grafana \
     -e "GF_INSTALL_PLUGINS=grafana-clock-panel,grafana-simple-json-datasource" \
     grafana/grafana
```

Grafana'yı başlatmak için http://localhost:3000 adresine (kullanıcı adı ve şifresi "admin" ile) girilir ve Data Source kısmına Prometheus eklenerek Prometheus'un konteynerler arasında erişilebileceği URL adresi (http://cprom:9090) yazılarak "Save & Test" düğmesine basılarak eklenir.

