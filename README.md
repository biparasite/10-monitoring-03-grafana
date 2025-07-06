# Домашнее задание к занятию 14 `«Средство визуализации Grafana»` - `Сулименков Алексей`

## Задание 1

1. Используя директорию [help](./help) внутри этого домашнего задания, запустите связку prometheus-grafana.
2. Зайдите в веб-интерфейс grafana, используя авторизационные данные, указанные в манифесте docker-compose.
3. Подключите поднятый вами prometheus, как источник данных.
4. Решение домашнего задания — скриншот веб-интерфейса grafana со списком подключенных Datasource.

---

## Ответ

<details> <summary>Grafana Web UI</summary>

![web](https://github.com/biparasite/10-monitoring-03-systems/blob/main/task_1.jpg "web")

</details>

---

## Задание 2

Изучите самостоятельно ресурсы:

1. [PromQL tutorial for beginners and humans](https://valyala.medium.com/promql-tutorial-for-beginners-9ab455142085).
2. [Understanding Machine CPU usage](https://www.robustperception.io/understanding-machine-cpu-usage).
3. [Introduction to PromQL, the Prometheus query language](https://grafana.com/blog/2020/02/04/introduction-to-promql-the-prometheus-query-language/).

Создайте Dashboard и в ней создайте Panels:

- утилизация CPU для nodeexporter (в процентах, 100-idle);
- CPULA 1/5/15;
- количество свободной оперативной памяти;
- количество места на файловой системе.

Для решения этого задания приведите promql-запросы для выдачи этих метрик, а также скриншот получившейся Dashboard.

---

## Ответ

- утилизация CPU для nodeexporter (в процентах, 100-idle);

```bash
avg by (instance) (irate(node_cpu_seconds_total{mode="idle"}[5m])) * 100
```

- CPULA 1/5/15;

1 min

```bash
avg(node_load1) / count(count(node_cpu_seconds_total) by (cpu))
```

5min

```bash
avg(node_load5) / count(count(node_cpu_seconds_total) by (cpu))
```

15min

```bash
avg(node_load15) / count(count(node_cpu_seconds_total) by (cpu))
```

- количество свободной оперативной памяти;

```bash
(node_memory_MemFree_bytes + node_memory_Buffers_bytes + node_memory_Cached_bytes) / 1024 / 1024
```

- количество места на файловой системе.

```bash
100 - ((avg_over_time(node_filesystem_avail_bytes{instance="nodeexporter:9100",job="nodeexporter",mountpoint="/",fstype!="rootfs"}[$__rate_interval]) * 100) / avg_over_time(node_filesystem_size_bytes{instance="nodeexporter:9100",job="nodeexporter",mountpoint="/",fstype!="rootfs"}[$__rate_interval]))
```

<details> <summary>Grafana dashboard</summary>

![web](https://github.com/biparasite/10-monitoring-03-systems/blob/main/task_1.jpg "web")

</details>

---

## Задание 3

1. Создайте для каждой Dashboard подходящее правило alert — можно обратиться к первой лекции в блоке «Мониторинг».
1. В качестве решения задания приведите скриншот вашей итоговой Dashboard.

---

## Ответ

Правило alert для LA 1 min > 0.1

```bash
avg(node_load1) / count(count(node_cpu_seconds_total) by (cpu)) > 0.1
```

<details> <summary>Grafana alert firing</summary>

![firing](https://github.com/biparasite/10-monitoring-03-systems/blob/main/task_3.jpg "firing")
![LA1](https://github.com/biparasite/10-monitoring-03-systems/blob/main/task_3.1.jpg "LA1")

</details>

---

## Задание 4

1. Сохраните ваш Dashboard.Для этого перейдите в настройки Dashboard, выберите в боковом меню «JSON MODEL». Далее скопируйте отображаемое json-содержимое в отдельный файл и сохраните его.
1. В качестве решения задания приведите листинг этого файла.

---

## Ответ

<details> <summary>Json Dashboard</summary>
```json
{
  "annotations": {
    "list": [
      {
        "builtIn": 1,
        "datasource": {
          "type": "grafana",
          "uid": "-- Grafana --"
        },
        "enable": true,
        "hide": true,
        "iconColor": "rgba(0, 211, 255, 1)",
        "name": "Annotations & Alerts",
        "type": "dashboard"
      }
    ]
  },
  "editable": true,
  "fiscalYearStartMonth": 0,
  "graphTooltip": 0,
  "id": 1,
  "links": [],
  "panels": [
    {
      "datasource": {
        "type": "prometheus",
        "uid": "eer3j1mgk4d8ge"
      },
      "fieldConfig": {
        "defaults": {
          "color": {
            "mode": "palette-classic"
          },
          "custom": {
            "axisBorderShow": false,
            "axisCenteredZero": false,
            "axisColorMode": "text",
            "axisLabel": "",
            "axisPlacement": "auto",
            "barAlignment": 0,
            "barWidthFactor": 0.6,
            "drawStyle": "line",
            "fillOpacity": 0,
            "gradientMode": "none",
            "hideFrom": {
              "legend": false,
              "tooltip": false,
              "viz": false
            },
            "insertNulls": false,
            "lineInterpolation": "linear",
            "lineWidth": 1,
            "pointSize": 5,
            "scaleDistribution": {
              "type": "linear"
            },
            "showPoints": "auto",
            "spanNulls": false,
            "stacking": {
              "group": "A",
              "mode": "none"
            },
            "thresholdsStyle": {
              "mode": "off"
            }
          },
          "mappings": [],
          "thresholds": {
            "mode": "absolute",
            "steps": [
              {
                "color": "green"
              },
              {
                "color": "red",
                "value": 80
              }
            ]
          },
          "unit": "percent"
        },
        "overrides": []
      },
      "gridPos": {
        "h": 8,
        "w": 12,
        "x": 0,
        "y": 0
      },
      "id": 4,
      "options": {
        "legend": {
          "calcs": [],
          "displayMode": "list",
          "placement": "bottom",
          "showLegend": true
        },
        "tooltip": {
          "hideZeros": false,
          "mode": "single",
          "sort": "none"
        }
      },
      "pluginVersion": "12.0.2",
      "targets": [
        {
          "editorMode": "code",
          "expr": "100 - ((avg_over_time(node_filesystem_avail_bytes{instance=\"nodeexporter:9100\",job=\"nodeexporter\",mountpoint=\"/\",fstype!=\"rootfs\"}[$__rate_interval]) * 100) / avg_over_time(node_filesystem_size_bytes{instance=\"nodeexporter:9100\",job=\"nodeexporter\",mountpoint=\"/\",fstype!=\"rootfs\"}[$__rate_interval]))\n",
          "legendFormat": "Disk usage %",
          "range": true,
          "refId": "A"
        }
      ],
      "title": "Disk Space Usage %",
      "type": "timeseries"
    },
    {
      "datasource": {
        "type": "prometheus",
        "uid": "eer3j1mgk4d8ge"
      },
      "description": "",
      "fieldConfig": {
        "defaults": {
          "color": {
            "mode": "palette-classic"
          },
          "custom": {
            "axisBorderShow": false,
            "axisCenteredZero": false,
            "axisColorMode": "text",
            "axisLabel": "100% IDLE",
            "axisPlacement": "auto",
            "barAlignment": 0,
            "barWidthFactor": 0.6,
            "drawStyle": "line",
            "fillOpacity": 0,
            "gradientMode": "none",
            "hideFrom": {
              "legend": false,
              "tooltip": false,
              "viz": false
            },
            "insertNulls": false,
            "lineInterpolation": "linear",
            "lineWidth": 1,
            "pointSize": 5,
            "scaleDistribution": {
              "type": "linear"
            },
            "showPoints": "auto",
            "spanNulls": false,
            "stacking": {
              "group": "A",
              "mode": "none"
            },
            "thresholdsStyle": {
              "mode": "off"
            }
          },
          "mappings": [],
          "thresholds": {
            "mode": "absolute",
            "steps": [
              {
                "color": "green"
              },
              {
                "color": "red",
                "value": 80
              },
              {
                "color": "#EAB839",
                "value": 90
              }
            ]
          },
          "unit": "percent"
        },
        "overrides": []
      },
      "gridPos": {
        "h": 8,
        "w": 12,
        "x": 12,
        "y": 0
      },
      "id": 1,
      "options": {
        "legend": {
          "calcs": [],
          "displayMode": "list",
          "placement": "bottom",
          "showLegend": true
        },
        "tooltip": {
          "hideZeros": false,
          "mode": "single",
          "sort": "none"
        }
      },
      "pluginVersion": "12.0.2",
      "targets": [
        {
          "datasource": {
            "type": "prometheus",
            "uid": "eer3j1mgk4d8ge"
          },
          "editorMode": "code",
          "expr": "avg by (instance) (irate(node_cpu_seconds_total{mode=\"idle\"}[5m])) * 100\n",
          "legendFormat": "CPU IDLE",
          "range": true,
          "refId": "A"
        }
      ],
      "title": "CPU IDLE",
      "transparent": true,
      "type": "timeseries"
    },
    {
      "datasource": {
        "type": "prometheus",
        "uid": "eer3j1mgk4d8ge"
      },
      "fieldConfig": {
        "defaults": {
          "color": {
            "mode": "palette-classic"
          },
          "custom": {
            "axisBorderShow": false,
            "axisCenteredZero": false,
            "axisColorMode": "text",
            "axisLabel": "",
            "axisPlacement": "auto",
            "barAlignment": 0,
            "barWidthFactor": 0.6,
            "drawStyle": "line",
            "fillOpacity": 0,
            "gradientMode": "none",
            "hideFrom": {
              "legend": false,
              "tooltip": false,
              "viz": false
            },
            "insertNulls": false,
            "lineInterpolation": "linear",
            "lineWidth": 1,
            "pointSize": 5,
            "scaleDistribution": {
              "type": "linear"
            },
            "showPoints": "auto",
            "spanNulls": false,
            "stacking": {
              "group": "A",
              "mode": "none"
            },
            "thresholdsStyle": {
              "mode": "off"
            }
          },
          "mappings": [],
          "thresholds": {
            "mode": "absolute",
            "steps": [
              {
                "color": "green"
              },
              {
                "color": "red",
                "value": 80
              }
            ]
          },
          "unit": "decmbytes"
        },
        "overrides": []
      },
      "gridPos": {
        "h": 8,
        "w": 12,
        "x": 0,
        "y": 8
      },
      "id": 3,
      "options": {
        "legend": {
          "calcs": [],
          "displayMode": "list",
          "placement": "bottom",
          "showLegend": true
        },
        "tooltip": {
          "hideZeros": false,
          "mode": "single",
          "sort": "none"
        }
      },
      "pluginVersion": "12.0.2",
      "targets": [
        {
          "disableTextWrap": false,
          "editorMode": "code",
          "exemplar": false,
          "expr": "node_memory_MemTotal_bytes{job=\"nodeexporter\"}/1024/1024",
          "format": "heatmap",
          "fullMetaSearch": false,
          "includeNullMetadata": true,
          "instant": false,
          "interval": "",
          "legendFormat": "Memory total",
          "range": true,
          "refId": "A",
          "useBackend": false
        },
        {
          "datasource": {
            "type": "prometheus",
            "uid": "eer3j1mgk4d8ge"
          },
          "editorMode": "code",
          "expr": "(node_memory_MemFree_bytes + node_memory_Buffers_bytes + node_memory_Cached_bytes) / 1024 / 1024\n",
          "hide": false,
          "instant": false,
          "legendFormat": "Free Memory",
          "range": true,
          "refId": "B"
        }
      ],
      "title": "Mem Free",
      "type": "timeseries"
    },
    {
      "datasource": {
        "type": "prometheus",
        "uid": "eer3j1mgk4d8ge"
      },
      "fieldConfig": {
        "defaults": {
          "color": {
            "mode": "palette-classic"
          },
          "custom": {
            "axisBorderShow": false,
            "axisCenteredZero": false,
            "axisColorMode": "text",
            "axisLabel": "LA 1/5/15nim",
            "axisPlacement": "auto",
            "barAlignment": 0,
            "barWidthFactor": 0.6,
            "drawStyle": "line",
            "fillOpacity": 0,
            "gradientMode": "none",
            "hideFrom": {
              "legend": false,
              "tooltip": false,
              "viz": false
            },
            "insertNulls": false,
            "lineInterpolation": "linear",
            "lineWidth": 1,
            "pointSize": 5,
            "scaleDistribution": {
              "type": "linear"
            },
            "showPoints": "auto",
            "spanNulls": false,
            "stacking": {
              "group": "A",
              "mode": "none"
            },
            "thresholdsStyle": {
              "mode": "off"
            }
          },
          "mappings": [],
          "thresholds": {
            "mode": "absolute",
            "steps": [
              {
                "color": "green"
              },
              {
                "color": "red",
                "value": 80
              }
            ]
          }
        },
        "overrides": []
      },
      "gridPos": {
        "h": 8,
        "w": 12,
        "x": 12,
        "y": 8
      },
      "id": 2,
      "options": {
        "legend": {
          "calcs": [],
          "displayMode": "list",
          "placement": "bottom",
          "showLegend": true
        },
        "tooltip": {
          "hideZeros": false,
          "mode": "single",
          "sort": "none"
        }
      },
      "pluginVersion": "12.0.2",
      "targets": [
        {
          "disableTextWrap": false,
          "editorMode": "code",
          "expr": "avg(node_load1) / count(count(node_cpu_seconds_total) by (cpu))",
          "fullMetaSearch": false,
          "includeNullMetadata": true,
          "legendFormat": "LA 1 min",
          "range": true,
          "refId": "A",
          "useBackend": false
        },
        {
          "datasource": {
            "type": "prometheus",
            "uid": "eer3j1mgk4d8ge"
          },
          "disableTextWrap": false,
          "editorMode": "code",
          "expr": "avg(node_load5) / count(count(node_cpu_seconds_total) by (cpu))",
          "fullMetaSearch": false,
          "hide": false,
          "includeNullMetadata": true,
          "instant": false,
          "legendFormat": "LA 5 min",
          "range": true,
          "refId": "B",
          "useBackend": false
        },
        {
          "datasource": {
            "type": "prometheus",
            "uid": "eer3j1mgk4d8ge"
          },
          "editorMode": "code",
          "expr": "avg(node_load15) / count(count(node_cpu_seconds_total) by (cpu))",
          "hide": false,
          "instant": false,
          "legendFormat": "LA 15 min",
          "range": true,
          "refId": "C"
        }
      ],
      "title": "LA on 1/5/15",
      "transparent": true,
      "type": "timeseries"
    }
  ],
  "preload": false,
  "refresh": "10s",
  "schemaVersion": 41,
  "tags": [],
  "templating": {
    "list": []
  },
  "time": {
    "from": "now-30m",
    "to": "now"
  },
  "timepicker": {},
  "timezone": "browser",
  "title": "Test",
  "uid": "d0600664-5c66-420e-a38b-b547e79dc6ff",
  "version": 17
}
```

</details>
