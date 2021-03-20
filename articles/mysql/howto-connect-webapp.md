---
title: Připojení k Azure App Service-Azure Database for MySQL
description: Pokyny, jak správně připojit existující Azure App Service k Azure Database for MySQL
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: how-to
ms.date: 3/18/2020
ms.openlocfilehash: 6355afe6ce5decbed029db4536b1b1b19f5a876c
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "94541500"
---
# <a name="connect-an-existing-azure-app-service-to-azure-database-for-mysql-server"></a>Připojit existující Azure App Service k serveru Azure Database for MySQL
Toto téma vysvětluje, jak připojit existující Azure App Service k serveru Azure Database for MySQL.

## <a name="before-you-begin"></a>Než začnete
Přihlaste se na [Azure Portal](https://portal.azure.com). Vytvořte server Azure Database for MySQL. Podrobnosti najdete v tématu [Postup vytvoření serveru Azure Database for MySQL z portálu](quickstart-create-mysql-server-database-using-azure-portal.md) nebo [Vytvoření Azure Database for MySQL serveru pomocí](quickstart-create-mysql-server-database-using-azure-cli.md)rozhraní příkazového řádku (CLI).

V současné době existují dvě řešení pro povolení přístupu z Azure App Service k Azure Database for MySQL. Obě řešení zahrnují nastavení pravidel brány firewall na úrovni serveru.

## <a name="solution-1---allow-azure-services"></a>Řešení 1 – povolení služeb Azure
Azure Database for MySQL poskytuje zabezpečení přístupu pomocí brány firewall k ochraně vašich dat. Když se připojujete z Azure App Service k Azure Database for MySQL serveru, pamatujte, že odchozí IP adresy App Service jsou v podstatě dynamické. Výběrem možnosti "povolení přístupu ke službám Azure" umožníte službě App Service připojit se k serveru MySQL.

1. V okně serveru MySQL pod záhlavím nastavení klikněte na **zabezpečení připojení** . otevře se okno zabezpečení připojení pro Azure Database for MySQL.

   :::image type="content" source="./media/howto-connect-webapp/1-connection-security.png" alt-text="Azure Portal – kliknutí na zabezpečení připojení":::

2. V v v nabídce **povolte přístup ke službám Azure** a pak klikněte **na** tlačítko **Uložit**.
   :::image type="content" source="./media/howto-connect-webapp/allow-azure.png" alt-text="Azure Portal – povolení přístupu k Azure":::

## <a name="solution-2---create-a-firewall-rule-to-explicitly-allow-outbound-ips"></a>Řešení 2 – Vytvoření pravidla brány firewall pro explicitní povolení odchozích IP adres
Můžete explicitně přidat všechny odchozí IP adresy vašich Azure App Service.

1. V okně App Service vlastnosti si prohlédněte svou **odchozí IP adresu**.

   :::image type="content" source="./media/howto-connect-webapp/2_1-outbound-ip-address.png" alt-text="Azure Portal – zobrazení odchozích IP adres":::

2. V okně zabezpečení připojení MySQL přidejte odchozí IP adresy jednu po jedné.

   :::image type="content" source="./media/howto-connect-webapp/2_2-add-explicit-ips.png" alt-text="Azure Portal – přidání explicitních IP adres":::

3. Nezapomeňte **Uložit** pravidla brány firewall.

I když se služba Azure App Service pokusí zachovat v průběhu času konstantní IP adresy, existují případy, kdy se IP adresy můžou změnit. K tomu může dojít například v případě, že dojde k recyklování nebo operacím škálování nebo když se v regionálních datových centrech Azure přidaly nové počítače ke zvýšení kapacity. Když se IP adresy změní, může aplikace v případě výpadku v události způsobit výpadky, které už se k serveru MySQL nepřipojí. Mějte na paměti, že při výběru jednoho z předchozích řešení je potřeba vzít na vědomí.

## <a name="ssl-configuration"></a>Konfigurace SSL
Azure Database for MySQL má standardně povolený protokol SSL. Pokud vaše aplikace nepoužívá pro připojení k databázi protokol SSL, musíte na serveru MySQL zakázat protokol SSL. Podrobnosti o tom, jak nakonfigurovat SSL, najdete v tématu [použití protokolu SSL s Azure Database for MySQL](howto-configure-ssl.md).

### <a name="django-pymysql"></a>Django (PyMySQL)
```python
DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.mysql',
        'NAME': 'quickstartdb',
        'USER': 'myadmin@mydemoserver',
        'PASSWORD': 'yourpassword',
        'HOST': 'mydemoserver.mysql.database.azure.com',
        'PORT': '3306',
        'OPTIONS': {
            'ssl': {'ssl-ca': '/var/www/html/BaltimoreCyberTrustRoot.crt.pem'}
        }
    }
}
```

## <a name="next-steps"></a>Další kroky
Další informace o připojovacích řetězcích najdete v tématu [připojovací řetězce](howto-connection-string.md).
