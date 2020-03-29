---
title: Připojení ke službě Azure App Service – databáze Azure pro MySQL
description: Pokyny, jak správně připojit existující službu Azure App Service k Azure Database for MySQL
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: ba75daa47ca1f77cd5828d13877238a64deeeb41
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80062459"
---
# <a name="connect-an-existing-azure-app-service-to-azure-database-for-mysql-server"></a>Připojení existující služby Azure App Service k azure databázi pro server MySQL
Toto téma vysvětluje, jak připojit existující službu Azure App Service k vašemu serveru Azure Database for MySQL.

## <a name="before-you-begin"></a>Než začnete
Přihlaste se k [portálu Azure](https://portal.azure.com). Vytvořte databázi Azure pro server MySQL. Podrobnosti najdete v článku [Jak vytvořit Azure Database pro MySQL server z portálu](quickstart-create-mysql-server-database-using-azure-portal.md) nebo Jak vytvořit Azure Database pro [MySQL server pomocí příkazového příkazu k příkazu k příkazu .](quickstart-create-mysql-server-database-using-azure-cli.md)

V současné době existují dvě řešení, která umožňují přístup ze služby Azure App Service do databáze Azure pro MySQL. Obě řešení zahrnují nastavení pravidel brány firewall na úrovni serveru.

## <a name="solution-1---allow-azure-services"></a>Řešení 1 – povolení služeb Azure
Azure Database for MySQL poskytuje zabezpečení přístupu pomocí brány firewall k ochraně dat. Při připojování ze služby Azure App Service k Azure Database for MySQL server mějte na paměti, že odchozí IP adresy služby App Service jsou dynamické povahy. Volba možnosti "Povolit přístup ke službám Azure" umožní tesejční službě připojit se k serveru MySQL.

1. Na okně serveru MySQL v záhlaví Nastavení klikněte na **Zabezpečení připojení** a otevřete okno Zabezpečení připojení pro Azure Database for MySQL.

   ![Portál Azure – klikněte na Zabezpečení připojení.](./media/howto-connect-webapp/1-connection-security.png)

2. Vyberte **ZAPNUTO** v **povolit přístup ke službám Azure**a pak **uložit**.
   ![Portál Azure – povolení přístupu k Azure](./media/howto-connect-webapp/allow-azure.png)

## <a name="solution-2---create-a-firewall-rule-to-explicitly-allow-outbound-ips"></a>Řešení 2 – Vytvoření pravidla brány firewall pro explicitní povolení odchozích IP služeb
Můžete explicitně přidat všechny odchozí IP adresy služby Azure App Service.

1. V okně Vlastnosti služby App Service zobrazte **odchozí IP adresu**.

   ![Portál Azure – zobrazení odchozích IP služeb](./media/howto-connect-webapp/2_1-outbound-ip-address.png)

2. V okně zabezpečení připojení MySQL přidejte odchozí IP adresy jeden po druhém.

   ![Portál Azure – přidání explicitních IP adresy](./media/howto-connect-webapp/2_2-add-explicit-ips.png)

3. Nezapomeňte **uložit** pravidla brány firewall.

Přestože azure app služba se pokusí zachovat IP adresy konstantní v průběhu času, existují případy, kdy IP adresy může změnit. K tomu může dojít například při recyklaci aplikace nebo operace škálování nebo při přidání nových počítačů v regionálních datových centrech Azure pro zvýšení kapacity. Když se IP adresy změní, aplikace může zaznamenat prostoje v případě, že se již nemůže připojit k serveru MySQL. Mějte tuto pozornost na paměti při výběru jednoho z předchozích řešení.

## <a name="ssl-configuration"></a>Konfigurace SSL
Azure Database for MySQL má ve výchozím nastavení povolený protokol SSL. Pokud vaše aplikace nepoužívá SSL pro připojení k databázi, pak je třeba zakázat SSL na serveru MySQL. Podrobnosti o konfiguraci protokolu SSL najdete [v tématu Použití protokolu SSL s databází Azure pro mySQL](howto-configure-ssl.md).

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
Další informace o připojovacích řetězcích naleznete [v připojovacích řetězcích](howto-connection-string.md).
