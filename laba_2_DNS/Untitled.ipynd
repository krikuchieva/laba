{
 "cells": [
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "# Исследование сетевых параметров публичных DNS серверов\n",
    "\n",
    "## Цель работы\n",
    "\n",
    "Проанализировать сетевые параметры публичных DNS серверов, сделать мотивированный вывод о предпочтительных серверах\n",
    "\n",
    "\n",
    "## ️Используемое ПО\n",
    "\n",
    "1.  Rstudio IDE\n",
    "2. `traceroute/tracert`\n",
    "3. `ping`\n",
    "4. `whois`\n",
    "\n",
    "## Исследуемые провайдеры DNS\n",
    "\n",
    "    1. Google Public DNS\n",
    "    2. Cloudflare DNS\n",
    "    3. OpenDNS\n",
    "    4. DNS вашего провайдера\n",
    "\n",
    "\n",
    "## Ход работы\n",
    "\n",
    "1. По исследуемым серверам собрать следующие данные:\n",
    "    - маршрут\n",
    "    - местоположение каждого узла маршрута к DNS-серверу\n",
    "    - организацию, владеющую каждым узлом маршрута к DNS-серверу\n",
    "    - среднюю RTT к DNS-серверу\n",
    "2. Выделить те узлы маршрута, которые вносят наибольшую временную задержку при передаче\n",
    "3. Сравнить сетевые параметры DNS серверов\n",
    "\n",
    "## Решение поставленнной цели\n",
    "\n",
    "**Google Public DNS** - интернет-сервис корпорации Google, предоставляющий общедоступные DNS-серверы. Открыт в декабре 2009 года. По словам компании, обеспечивает ускорение загрузки веб-страниц за счет повышения эффективности кэширования данных, а также улучшенную защиту от спуфинга."
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 3,
   "metadata": {},
   "outputs": [
    {
     "name": "stdout",
     "output_type": "stream",
     "text": [
      "Active code page: 65001\n"
     ]
    }
   ],
   "source": [
    "!chcp 65001"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 4,
   "metadata": {},
   "outputs": [
    {
     "name": "stdout",
     "output_type": "stream",
     "text": [
      "\n",
      "Tracing route to dns.google [8.8.8.8]\n",
      "over a maximum of 30 hops:\n",
      "\n",
      "  1     1 ms     1 ms    <1 ms  192.168.0.1 \n",
      "  2     1 ms     1 ms     1 ms  172.18.0.1 \n",
      "  3     2 ms     1 ms     1 ms  gw-core.gptel.ru [77.73.25.1] \n",
      "  4     2 ms     2 ms     1 ms  77.94.162.77 \n",
      "  5     1 ms     1 ms     2 ms  ae3-nostromo-mtt-msk.naukanet.ru [77.94.160.52] \n",
      "  6     8 ms     4 ms     4 ms  209.85.148.20 \n",
      "  7     2 ms     2 ms     2 ms  108.170.250.66 \n",
      "  8    19 ms    19 ms    20 ms  209.85.255.136 \n",
      "  9    48 ms    20 ms    17 ms  216.239.54.50 \n",
      " 10     *        *        *     Request timed out.\n",
      " 11     *        *        *     Request timed out.\n",
      " 12     *        *        *     Request timed out.\n",
      " 13     *        *        *     Request timed out.\n",
      " 14     *        *        *     Request timed out.\n",
      " 15     *        *        *     Request timed out.\n",
      " 16     *        *        *     Request timed out.\n",
      " 17     *        *        *     Request timed out.\n",
      " 18     *        *        *     Request timed out.\n",
      " 19    19 ms    18 ms    20 ms  dns.google [8.8.8.8] \n",
      "\n",
      "Trace complete.\n"
     ]
    }
   ],
   "source": [
    "!tracert 8.8.8.8"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "RTT = 19 ms\n",
    "\n",
    "|Узел|Месторасположение| Организация|\n",
    "|-|-|-|\n",
    "|  192.168.0.1|Москва|Домашний роутер|\n",
    "|  172.18.0.1|Москва|Гран При Телеком|\n",
    "|  gw-core.gptel.ru [77.73.25.1]|Гран При Телеком|\n",
    "|  77.94.162.77|Москва|Наука-Связь|\n",
    "|  ae3-nostromo-mtt-msk.naukanet.ru [77.94.160.52]|Москва|Наука-Связь|\n",
    "|  209.85.148.20|USA|Google LLC|\n",
    "|  108.170.250.66|Mountain View, USA|Google LLC|\n",
    "|  209.85.255.136|USA|Google LLC|__\n",
    "|  216.239.54.50|USA|Google LLC|\n",
    "|  Превышен интервал ожидания для запроса.|\n",
    "|  Превышен интервал ожидания для запроса.|\n",
    "|  Превышен интервал ожидания для запроса.|\n",
    "|  Превышен интервал ожидания для запроса.|\n",
    "|  Превышен интервал ожидания для запроса.|\n",
    "|  Превышен интервал ожидания для запроса.|\n",
    "|  Превышен интервал ожидания для запроса.|\n",
    "|  Превышен интервал ожидания для запроса.|\n",
    "|  Превышен интервал ожидания для запроса.|\n",
    "|  dns.google [8.8.8.8]|Нью-Йорк, USA|Google LLC|"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "**Cloudflare** — американская компания, предоставляющая услуги CDN, защиту от DDoS-атак, безопасный доступ к ресурсам и серверы DNS. "
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 5,
   "metadata": {},
   "outputs": [
    {
     "name": "stdout",
     "output_type": "stream",
     "text": [
      "\n",
      "Tracing route to one.one.one.one [1.1.1.1]\n",
      "over a maximum of 30 hops:\n",
      "\n",
      "  1    <1 ms    <1 ms    <1 ms  192.168.0.1 \n",
      "  2     2 ms    <1 ms    <1 ms  172.18.0.1 \n",
      "  3     2 ms     1 ms     1 ms  gw-core.gptel.ru [77.73.25.1] \n",
      "  4     1 ms     1 ms     1 ms  gptel145.cln.net [79.98.8.145] \n",
      "  5     2 ms     2 ms     2 ms  jun0-mr-vl7.cln.net [79.98.8.254] \n",
      "  6    19 ms    19 ms    19 ms  netnod-ix-ge-a-sth-1500.cloudflare.com [194.68.123.246] \n",
      "  7    20 ms    19 ms    19 ms  one.one.one.one [1.1.1.1] \n",
      "\n",
      "Trace complete.\n"
     ]
    }
   ],
   "source": [
    "!tracert 1.1.1.1"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "RTT = 19\n",
    "\n",
    "|Узел|Месторасположение| Организация|\n",
    "|-|-|-|\n",
    "|  192.168.0.1|Москва|Домашний роутер|\n",
    "|  172.18.0.1|Москва|Гран При Телеком|\n",
    "|  gw-core.gptel.ru [77.73.25.1]|Москва|Гран При Телеком|\n",
    "|  gptel145.cln.net [79.98.8.145]|Москва|CLN|\n",
    "|  jun0-mr-vl7.cln.net [79.98.8.254]|Москва|CLN|\n",
    "|  netnod-ix-ge-a-sth-1500.cloudflare.com [194.68.123.246]|Borgholm, Sweden|Stockholm Interconnect GigEth|\n",
    "|  one.one.one.one [1.1.1.1]|\tResearch, Australia| Cloudflare DNS|\n"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "**OpenDNS** — интернет-служба, предоставляющая общедоступные DNS-серверы. Имеет платный и бесплатный режим, может исправлять опечатки в набираемых адресах, фильтровать фишинговые сайты, в случае набора неправильных запросов может предлагать страницу с поиском и рекламой.\n"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 6,
   "metadata": {},
   "outputs": [
    {
     "name": "stdout",
     "output_type": "stream",
     "text": [
      "\n",
      "Tracing route to resolver2.opendns.com [208.67.220.220]\n",
      "over a maximum of 30 hops:\n",
      "\n",
      "  1    <1 ms    <1 ms    <1 ms  192.168.0.1 \n",
      "  2     1 ms     1 ms     1 ms  172.18.0.1 \n",
      "  3     1 ms     1 ms     1 ms  gw-core.gptel.ru [77.73.25.1] \n",
      "  4     1 ms     2 ms     1 ms  gptel145.cln.net [79.98.8.145] \n",
      "  5     3 ms     1 ms     1 ms  jun0-mr-vl7.cln.net [79.98.8.254] \n",
      "  6     2 ms     1 ms     1 ms  ae11-747.RT.MR.MSK.RU.retn.net [87.245.253.18] \n",
      "  7    76 ms    39 ms    40 ms  ae3-8.RT.TC2.AMS.NL.retn.net [87.245.233.17] \n",
      "  8    47 ms    47 ms    53 ms  peer1.rtr1.ams.opendns.com [80.249.208.88] \n",
      "  9    45 ms    47 ms    47 ms  resolver2.opendns.com [208.67.220.220] \n",
      "\n",
      "Trace complete.\n"
     ]
    }
   ],
   "source": [
    "!tracert 208.67.220.220"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "RTT = 46\n",
    "\n",
    "|Узел|Месторасположение| Организация|\n",
    "|-|-|-|\n",
    "| 192.168.0.1|Москва|Домашний роутер|\n",
    "| 172.18.0.1|Москва|Гран При Телеком|\n",
    "| gw-core.gptel.ru [77.73.25.1]|Москва|Гран При Телеком|\n",
    "| gptel145.cln.net [79.98.8.145]|Москва|CLN|\n",
    "| jun0-mr-vl7.cln.net [79.98.8.254]|Москва|CLN|\n",
    "| ae11-747.RT.MR.MSK.RU.retn.net [87.245.253.18]|Москва|ReTN external interconnections in Moscow|\n",
    "| ae3-8.RT.TC2.AMS.NL.retn.net [87.245.233.17]|Москва|Grand Prix Telecom|\n",
    "| peer1.rtr1.ams.opendns.com [80.249.208.88]| Amsterdam|AMS-IX Amsterdam IPv4 Peering (ISP) LAN|\n",
    "| resolver2.opendns.com [208.67.220.220]|San Francisco|OpenDNS, LLC|\n",
    "\n",
    "**Гран При Телеком** - оператор услуг связи, поставщик и интегратор АТС оборудования."
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 7,
   "metadata": {},
   "outputs": [
    {
     "name": "stdout",
     "output_type": "stream",
     "text": [
      "\n",
      "Tracing route to ns.gptel.ru [77.73.24.2]\n",
      "over a maximum of 30 hops:\n",
      "\n",
      "  1     1 ms     1 ms     1 ms  192.168.0.1 \n",
      "  2     2 ms     1 ms     1 ms  172.18.0.1 \n",
      "  3     1 ms     1 ms     1 ms  gw-aggr1.gptel.ru [77.73.25.6] \n",
      "  4     1 ms     1 ms     1 ms  ns.gptel.ru [77.73.24.2] \n",
      "\n",
      "Trace complete.\n"
     ]
    }
   ],
   "source": [
    "!tracert 77.73.24.2"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "RTT = 1\n",
    "\n",
    "|Узел|Месторасположение| Организация|\n",
    "|-|-|-|\n",
    "| 192.168.0.1|Москва|Домашний роутер|\n",
    "| 172.18.0.1|Москва|Гран При Телеком|\n",
    "| gw-aggr1.gptel.ru [77.73.25.6]|Москва|Гран При Телеком|\n",
    "| ns.gptel.ru [77.73.24.2]|Москва|network for GRANDPRIX Telecom|"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "**График распределения времени до DNS - серверов**"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 70,
   "metadata": {},
   "outputs": [
    {
     "data": {
      "image/png": "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\n",
      "text/plain": [
       "<Figure size 432x288 with 1 Axes>"
      ]
     },
     "metadata": {
      "needs_background": "light"
     },
     "output_type": "display_data"
    }
   ],
   "source": [
    "import matplotlib.pyplot as plt\n",
    "plt.title(\"Время ответа на каждом шаге к DNS серверу\") \n",
    "plt.xlabel(\"Шаг\")\n",
    "plt.ylabel(\"Время\") # ось ординат\n",
    "plt.plot([1,2,3,4,5,6,7,8,9,10,11,12,13,14,15,16,17,18,19],[1,1,1,1,1,1,2,20,17,0,0,0,0,0,0,0,0,0,19],'r')\n",
    "plt.plot([1,2,3,4,5,6,7],[1,2,2,1,2,19,20],'g')\n",
    "plt.plot([1,2,3,4,5,6,7,8,9],[1,1,1,1,3,2,76,47,45],'y')\n",
    "plt.plot([1,2,3,4],[1,2,1,1],'b')\n",
    "plt.grid() \n",
    "plt.show()\n"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "#### Вывод\n",
    "Необходимо пользоваться быстрым и проверенным DNS-сервером."
   ]
  },
  {
   "cell_type": "code",
   "execution_count": null,
   "metadata": {},
   "outputs": [],
   "source": []
  }
 ],
 "metadata": {
  "kernelspec": {
   "display_name": "Python 3",
   "language": "python",
   "name": "python3"
  },
  "language_info": {
   "codemirror_mode": {
    "name": "ipython",
    "version": 3
   },
   "file_extension": ".py",
   "mimetype": "text/x-python",
   "name": "python",
   "nbconvert_exporter": "python",
   "pygments_lexer": "ipython3",
   "version": "3.7.6"
  }
 },
 "nbformat": 4,
 "nbformat_minor": 4
}
