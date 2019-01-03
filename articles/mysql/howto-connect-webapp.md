---
title: Připojit existující služby Azure App Service do služby Azure Database for MySQL
description: Pokyny, jak správně připojit existující aplikaci služby Azure do služby Azure Database for MySQL
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 09/26/2018
ms.openlocfilehash: eb2fee7c76bcf29aee2dcd70d7975d7631bb23f6
ms.sourcegitcommit: 71ee622bdba6e24db4d7ce92107b1ef1a4fa2600
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/17/2018
ms.locfileid: "53548948"
---
# <a name="connect-an-existing-azure-app-service-to-azure-database-for-mysql-server"></a>Připojit existující aplikaci služby Azure do služby Azure Database pro MySQL server
Toto téma vysvětluje, jak připojit stávající Azure App Service pro váš server Azure Database for MySQL.

## <a name="before-you-begin"></a>Před zahájením
Přihlaste se k webu [Azure Portal](https://portal.azure.com). Vytvoření serveru Azure Database for MySQL. Podrobnosti najdete v [postupy vytvoření Azure Database for MySQL serveru z portálu](quickstart-create-mysql-server-database-using-azure-portal.md) nebo [vytvoření – Azure Database for MySQL pomocí rozhraní příkazového řádku](quickstart-create-mysql-server-database-using-azure-cli.md).

Aktuálně jsou dostupné dvě řešení pro povolení přístupu ze služby Azure App Service ke službě Azure Database for MySQL. Obě řešení zahrnují nastavení pravidla brány firewall na úrovni serveru.

## <a name="solution-1---allow-azure-services"></a>Řešení 1 - povolit službám Azure
Azure Database for MySQL poskytuje zabezpečení přístupu k ochraně vašich dat pomocí brány firewall. Při připojování ze služby Azure App Service do služby Azure Database for MySQL serveru, mějte na paměti, že se kvůli dynamické povaze odchozí IP adresy App Service. Pokud vyberete možnost "Povolit přístup ke službám Azure" vám umožní služby app service pro připojení k serveru MySQL.

1. V okně serveru MySQL, v části Nastavení záhlaví, klikněte na tlačítko **zabezpečení připojení** otevřete okno zabezpečení připojení pro službu Azure Database for MySQL.

   ![Portál Azure – zabezpečení připojení klikněte na](./media/howto-connect-webapp/1-connection-security.png)

2. Vyberte **ON** v **povolit přístup ke službám Azure**, pak **Uložit**.
   ![Azure portal – povolit Azure access](./media/howto-connect-webapp/allow-azure.png)

## <a name="solution-2---create-a-firewall-rule-to-explicitly-allow-outbound-ips"></a>Řešení 2 – Vytvoření pravidla brány firewall můžete výslovně povolit odchozí IP adresy
Můžete explicitně přidat všechny odchozí IP adresy služby Azure App Service.

1. V okně Vlastnosti služby App Service zobrazit vaše **ODCHOZÍ IP adresa**.

   ![Azure portal – zobrazení odchozí IP adresy](./media/howto-connect-webapp/2_1-outbound-ip-address.png)

2. V okně zabezpečení připojení k MySQL přidáte odchozí IP adresy jeden po druhém.

   ![Azure portal – přidání explicitních IP adres](./media/howto-connect-webapp/2_2-add-explicit-ips.png)

3. Nezapomeňte **Uložit** pravidla brány firewall.

Když se službou Azure App service se pokusí uchovat konstantní IP adresy v čase, existují případy, ve kterém může změna IP adresy. Například tato situace může nastat pokud dojde k události recyklace aplikace nebo operace škálování, nebo při přidávání nových počítačů v Azure regionálního datového centra ke zvýšení kapacity. Při změně adres IP, aplikace může dojít k výpadku v případě, že už se může připojit k serveru MySQL. Mějte tento zvážit při výběru některého z předchozích řešení.

## <a name="ssl-configuration"></a>Konfigurace SSL
Azure Database for MySQL je protokol SSL standardně povolený. Pokud vaše aplikace nepoužívá protokol SSL pro připojení k databázi, budete muset zakázat SSL na serveru MySQL. Podrobnosti o tom, jak konfigurovat jím protokol SSL najdete v tématu [pomocí protokolu SSL s využitím Azure Database for MySQL](howto-configure-ssl.md).

## <a name="next-steps"></a>Další postup
Další informace o připojovacích řetězcích najdete [připojovací řetězce](howto-connection-string.md).
