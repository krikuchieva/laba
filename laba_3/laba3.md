# **Лабораторная работа №3. Сбор информации о параметрах сетевой инфраструктуры компаний**

## **1. Цель**

1. Выбрать 15 компаний для исследования
2. Написать код для автоматизированного сбора информации о доменах
3. Собрать информацию c помощью этого программного кода
4. Составить таблицу

## ️**2. Исходные данные** 

1. Ноутбук HP Pavilion15 c основной ОС Windows 10
2. VM Ubuntu 16 LTE
3. Domain - address 15 компаний

## **3. Используемое ПО**
1. JupyterLab
2. whois
3. nslookup
4. nmap

## ️**4. Варианты решения задач**
1. Собрать информацию вручную с помощью веб-браузера, инструментов whois, dig, nmap и т.д.
2. Использоавть интегрированные инструменты такие как SpiderFoot, Maltego CE, Datasploit, Recon-ng
3. Самостоятельно разработать (для образовательных целей) автоматизированное решение для сбора информации.


## ️**4.1. Общий ход выполнения работы** 
1. Написание функции/скрипта для сбора требуемой информации
2. Сбор информации по компаниям

## ️**4.2. Разработка средства сбора информации** 


```python
import subprocess
import re
from prettytable import PrettyTable

class Site_obj():
    def __init__(self, domain_name):
        self.domain = domain_name
        self.ip_address = self.ip_address_def(domain_name)
        self.ip_netblock = self.ip_netblock_def(self.ip_address)
        self.address = self.address_def(self.ip_address[0])
        self.hosting_phone = self.hosting_phone_def(self.ip_address[0])
        self.ports = self.ports_def(self.ip_address[0])
    
    def ip_address_def(self, domain_name):
        while True:
            res = subprocess.Popen(["nslookup" , domain_name],  stdout=subprocess.PIPE)
            output, self.errors = res.communicate()
            res.wait()
            result = re.findall(r'Address: (\d{,3}\.\d{,3}\.\d{,3}\.\d{,3})', output)
            if result != []:
                break
        return result

    def ip_netblock_def(self, ip_address):
        result = []
        for i in range(len(ip_address)):
            res = subprocess.Popen(["whois" , ip_address[i]],  stdout=subprocess.PIPE)
            output, self.errors = res.communicate()
            res.wait()
            output = re.sub('route', 'CIDR', output)
            result.extend(re.findall(r'CIDR:\s*(.*)', output ))
        return result   
        
    def address_def(self, ip_address):
        result = []
        res = subprocess.Popen(["whois" , ip_address],  stdout=subprocess.PIPE)
        output, self.errors = res.communicate()
        res.wait()
        country = re.findall(r'[C,c]ountry:\s*(.*)', output)
        result.append(country[0])
        city = re.findall(r'City:\s*(.*)', output)
        address = re.findall(r'Address:\s*(.*)', output)
        result.append(city)
        result.append(address)
        return result
            
    def hosting_phone_def(self, ip_address):
        result = []      
        res = subprocess.Popen(["whois" , ip_address],  stdout=subprocess.PIPE)
        output, self.errors = res.communicate()
        res.wait()
        result.append(re.findall(r'Organization:\s*(.*)', output))
        result.append(re.findall(r'OrgTechPhone:\s*(.*)', output))
        return result
        
    def ports_def(self, ip_address):
        res = subprocess.Popen(['nmap','-F', ip_address] , shell=False, stdout=subprocess.PIPE )
        res.wait()
        grep = subprocess.Popen(['grep', 'open'], stdin=res.stdout,  stdout=subprocess.PIPE)
        grep.wait()
        output, errors = grep.communicate()
        result = re.findall(r'\d{,5}', output)
        return result         
  
```

## **4.3  Сбор информации по компаниям**
Запустим наш программный код и выведем результат


```python
site = ['Slashdot.org' ,'Mozilla.org' ,'Eu.wikipedia.org' , 'Github.com',  'Sourceforge.net', 'Apache.org','Notepad-plus-plus.org','Slashdot.org','Mozilla.org',
       'Addons.mozilla.org', 'Nginx.org', 'Launchpad.net', 'Portableapps.com',   'Codeplex.com', 'About.gitlab.com', 'Musescore.org' , 'Curl.haxx.se' ]
#list_site_obj = [Site_obj(site[i]) for i in range(len(site)) ] #Create list objects "site"
x = PrettyTable()
x.field_names = ["Domain name", " Ip_address" , "Ip_netblock", "Country", "Address", "Phone", "Ports" ]
for i in range(15):
    x.add_row([list_site_obj[i].domain, '\n'.join(list_site_obj[i].ip_address)+ '\n Hosting \n\n' + '\n'.join(str(list_site_obj[i].hosting_phone[0])[2:-2].split(' ')), 
               '\n'.join((list_site_obj[i].ip_netblock[0]).split(', ')), list_site_obj[i].address[0], '\n'.join(str(list(list_site_obj[i].address[2]))[2:-2].replace('\'','').split(' ')),
               '\n'.join(set(list_site_obj[i].hosting_phone[1])) ,'\n'.join(set(list_site_obj[i].ports))])
    x.add_row(['\n', '\n', '\n', '\n', '\n', '\n', '\n'])
print(x)

```

    +-----------------------+----------------+------------------+---------+-----------+------------------+-------+
    |      Domain name      |   Ip_address   |   Ip_netblock    | Country |  Address  |      Phone       | Ports |
    +-----------------------+----------------+------------------+---------+-----------+------------------+-------+
    |      Slashdot.org     | 216.105.38.15  | 216.105.32.0/20  |    US   |    9725   | +1-888-327-8375  |       |
    |                       |    Hosting     |                  |         |  Scranton |                  |  443  |
    |                       |                |                  |         |    Road   |                  |   80  |
    |                       |    Internet    |                  |         |           |                  |       |
    |                       |    Express     |                  |         |           |                  |       |
    |                       |     (IXPR)     |                  |         |           |                  |       |
    |                       |                |                  |         |           |                  |       |
    |                       |                |                  |         |           |                  |       |
    |      Mozilla.org      | 63.245.208.195 | 63.245.208.0/20  |    US   |    331    | +1-650-903-0800  |       |
    |                       |    Hosting     |                  |         |    East   |                  |  443  |
    |                       |                |                  |         |   Evelyn  |                  |   80  |
    |                       |    Mozilla     |                  |         |   Ave.,   |                  |       |
    |                       |  Corporation   |                  |         |    1100   |                  |       |
    |                       |   (MOZIL-1)    |                  |         |   North   |                  |       |
    |                       |                |                  |         |   Market  |                  |       |
    |                       |                |                  |         |    Blvd   |                  |       |
    |                       |                |                  |         |           |                  |       |
    |                       |                |                  |         |           |                  |       |
    |    Eu.wikipedia.org   | 91.198.174.192 | 91.198.174.0/24  |    NL   |           |                  |       |
    |                       |    Hosting     |                  |         |           |                  |  443  |
    |                       |                |                  |         |           |                  |   80  |
    |                       |                |                  |         |           |                  |       |
    |                       |                |                  |         |           |                  |       |
    |                       |                |                  |         |           |                  |       |
    |       Github.com      |  140.82.118.4  | 140.82.112.0/20  |    US   |     88    | +1-415-735-4488  |       |
    |                       |    Hosting     |                  |         |   Colin   |                  |  443  |
    |                       |                |                  |         |     P     |                  |   80  |
    |                       |    GitHub,     |                  |         |   Kelly   |                  |   22  |
    |                       |      Inc.      |                  |         |     Jr    |                  |       |
    |                       |    (GITHU)     |                  |         |   Street  |                  |       |
    |                       |                |                  |         |           |                  |       |
    |                       |                |                  |         |           |                  |       |
    |    Sourceforge.net    | 216.105.38.13  | 216.105.32.0/20  |    US   |    9725   | +1-888-327-8375  |       |
    |                       |    Hosting     |                  |         |  Scranton |                  |  443  |
    |                       |                |                  |         |    Road   |                  |   80  |
    |                       |    Internet    |                  |         |           |                  |       |
    |                       |    Express     |                  |         |           |                  |       |
    |                       |     (IXPR)     |                  |         |           |                  |       |
    |                       |                |                  |         |           |                  |       |
    |                       |                |                  |         |           |                  |       |
    |       Apache.org      |   40.79.78.1   |   40.96.0.0/12   |    US   |    One    | +1-425-882-8080  |       |
    |                       |  95.216.24.32  |  40.125.0.0/17   |         | Microsoft |                  |  443  |
    |                       |    Hosting     |   40.76.0.0/14   |         |    Way    |                  |   80  |
    |                       |                |   40.74.0.0/15   |         |           |                  |   22  |
    |                       |   Microsoft    |  40.112.0.0/13   |         |           |                  |       |
    |                       |  Corporation   |   40.80.0.0/12   |         |           |                  |       |
    |                       |     (MSFT)     |  40.120.0.0/14   |         |           |                  |       |
    |                       |                |  40.124.0.0/16   |         |           |                  |       |
    |                       |                |                  |         |           |                  |       |
    |                       |                |                  |         |           |                  |       |
    | Notepad-plus-plus.org |  104.31.89.28  |  104.16.0.0/12   |    US   |    101    | +1-650-319-8930  |       |
    |                       |  104.31.88.28  |                  |         |  Townsend |                  |  443  |
    |                       |    Hosting     |                  |         |   Street  |                  |   80  |
    |                       |                |                  |         |           |                  |  8080 |
    |                       |  Cloudflare,   |                  |         |           |                  |       |
    |                       |      Inc.      |                  |         |           |                  |       |
    |                       |   (CLOUD14)    |                  |         |           |                  |       |
    |                       |                |                  |         |           |                  |       |
    |                       |                |                  |         |           |                  |       |
    |      Slashdot.org     | 216.105.38.15  | 216.105.32.0/20  |    US   |    9725   | +1-888-327-8375  |       |
    |                       |    Hosting     |                  |         |  Scranton |                  |   80  |
    |                       |                |                  |         |    Road   |                  |       |
    |                       |    Internet    |                  |         |           |                  |       |
    |                       |    Express     |                  |         |           |                  |       |
    |                       |     (IXPR)     |                  |         |           |                  |       |
    |                       |                |                  |         |           |                  |       |
    |                       |                |                  |         |           |                  |       |
    |      Mozilla.org      | 63.245.208.195 | 63.245.208.0/20  |    US   |    331    | +1-650-903-0800  |       |
    |                       |    Hosting     |                  |         |    East   |                  |  443  |
    |                       |                |                  |         |   Evelyn  |                  |   80  |
    |                       |    Mozilla     |                  |         |   Ave.,   |                  |       |
    |                       |  Corporation   |                  |         |    1100   |                  |       |
    |                       |   (MOZIL-1)    |                  |         |   North   |                  |       |
    |                       |                |                  |         |   Market  |                  |       |
    |                       |                |                  |         |    Blvd   |                  |       |
    |                       |                |                  |         |           |                  |       |
    |                       |                |                  |         |           |                  |       |
    |   Addons.mozilla.org  |  52.27.156.1   |   52.0.0.0/11    |    US   |    410    | +1-206-266-4064  |       |
    |                       | 35.161.136.160 |                  |         |   Terry   |                  |  443  |
    |                       | 35.162.102.192 |                  |         |    Ave    |                  |   80  |
    |                       |    Hosting     |                  |         |     N.    |                  |       |
    |                       |                |                  |         |           |                  |       |
    |                       |     Amazon     |                  |         |           |                  |       |
    |                       |  Technologies  |                  |         |           |                  |       |
    |                       |      Inc.      |                  |         |           |                  |       |
    |                       |   (AT-88-Z)    |                  |         |           |                  |       |
    |                       |                |                  |         |           |                  |       |
    |                       |                |                  |         |           |                  |       |
    |       Nginx.org       | 95.211.80.227  |  95.211.0.0/16   |    NL   |           |                  |       |
    |                       |  62.210.92.35  |                  |         |           |                  |  443  |
    |                       |    Hosting     |                  |         |           |                  |   80  |
    |                       |                |                  |         |           |                  |   53  |
    |                       |                |                  |         |           |                  |   22  |
    |                       |                |                  |         |           |                  |       |
    |                       |                |                  |         |           |                  |       |
    |     Launchpad.net     | 91.189.89.223  |  91.189.89.0/24  |    GB   |           |                  |       |
    |                       | 91.189.89.222  |                  |         |           |                  |  443  |
    |                       |    Hosting     |                  |         |           |                  |   80  |
    |                       |                |                  |         |           |                  |       |
    |                       |                |                  |         |           |                  |       |
    |                       |                |                  |         |           |                  |       |
    |                       |                |                  |         |           |                  |       |
    |    Portableapps.com   | 104.239.166.87 | 104.239.128.0/17 |    US   |     1     | +1-210-312-4000  |       |
    |                       |    Hosting     |                  |         | Fanatical |                  |  443  |
    |                       |                |                  |         |   Place,  |                  |   80  |
    |                       |   Rackspace    |                  |         |    5000   |                  |       |
    |                       |    Hosting     |                  |         |   Walzem  |                  |       |
    |                       |   (RACKS-8)    |                  |         |    Rd.    |                  |       |
    |                       |                |                  |         |           |                  |       |
    |                       |                |                  |         |           |                  |       |
    |      Codeplex.com     | 52.183.82.125  |  52.152.0.0/13   |    US   |    One    | +1-425-882-8080  |       |
    |                       |    Hosting     |  52.148.0.0/14   |         | Microsoft |                  |  443  |
    |                       |                |  52.145.0.0/16   |         |    Way    |                  |   80  |
    |                       |   Microsoft    |  52.146.0.0/15   |         |           |                  |       |
    |                       |  Corporation   |  52.160.0.0/11   |         |           |                  |       |
    |                       |     (MSFT)     |                  |         |           |                  |       |
    |                       |                |                  |         |           |                  |       |
    |                       |                |                  |         |           |                  |       |
    |    About.gitlab.com   |  151.101.2.49  |  151.101.0.0/16  |    US   |     PO    | +1-415-404-9374  |       |
    |                       | 151.101.66.49  |                  |         |    Box    |                  |  443  |
    |                       | 151.101.130.49 |                  |         |   78266   |                  |   80  |
    |                       | 151.101.194.49 |                  |         |           |                  |       |
    |                       |    Hosting     |                  |         |           |                  |       |
    |                       |                |                  |         |           |                  |       |
    |                       |     Fastly     |                  |         |           |                  |       |
    |                       |   (SKYCA-3)    |                  |         |           |                  |       |
    |                       |                |                  |         |           |                  |       |
    |                       |                |                  |         |           |                  |       |
    +-----------------------+----------------+------------------+---------+-----------+------------------+-------+


## **5. ️Оценка результата**
В результате выполнения задачи, нами было получено достаточно универсальное решение по сбору информации о доменах.

## **6 Выводы**
При выборе варианта решения поставленной задачи, следует предварительно оценить объем повторяющихся действий и рассмотреть способы автоматизации.


```python

```


```python

```
