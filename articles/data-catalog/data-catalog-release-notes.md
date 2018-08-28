---
title: Poznámky k verzi Azure Data Catalog
description: Zpráva k vydání verze pro Azure Data Catalog.
services: data-catalog
author: steelanddata
ms.author: maroche
ms.assetid: 3aca9c49-45a4-4352-92e6-bd25ee3eacf7
ms.service: data-catalog
ms.topic: conceptual
ms.date: 01/18/2018
ms.openlocfilehash: 539ef4f591a1ef0c6ab344e1d93a750133d9e089
ms.sourcegitcommit: 161d268ae63c7ace3082fc4fad732af61c55c949
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/27/2018
ms.locfileid: "43053489"
---
# <a name="azure-data-catalog-release-notes"></a>Poznámky k verzi Azure Data Catalog
## <a name="notes-for-the-november-20-2015-release-of-azure-data-catalog"></a>Poznámky k 20. listopadu 2015 verzi služby Azure Data Catalog
### <a name="opening-data-sources-in-power-bi-desktop"></a>Otevření zdroje dat v Power BI Desktopu
Při použití možnosti "Otevřít v Power BI Desktopu" z **Azure Data Catalog** portálu, uživatele může dojít k jedné ze dvou problémů v aplikaci Power BI Desktopu:

* Zobrazí se dialogové okno s názvem "Nelze do otevřeného dokumentu"
* Aplikace Power BI Desktopu se otevře, ale soubor je pravděpodobně být prázdné.

Pro každé situaci, problém lze vyřešit stažením a instalací nejnovější verzi Power BI Desktopu z [PowerBI.com](https://powerbi.com).

## <a name="notes-for-the-november-13-2015-release-of-azure-data-catalog"></a>Poznámky k 13. listopadu 2015 verzi služby Azure Data Catalog
### <a name="registering-and-connecting-to-teradata"></a>Registrace a připojování k Teradata
Při připojování k Teradata zdroje dat uživatelé musí mít nainstalovaného správné ovladače Teradata ODBC, které odpovídají bitové verze (32bitová nebo 64bitová verze) softwaru používán.

Od této ADC datum vydání, nejnovější [ovladač Teradata ODBC pro windows (verzi 15.10)](http://downloads.teradata.com/download/connectivity/odbc-driver/windows) je kompatibilní s aplikací Office 2013, ale ne s Office 2016.

## <a name="notes-for-the-july-13-2015-release-of-azure-data-catalog"></a>Poznámky k 13. července 2015 verzi služby Azure Data Catalog
### <a name="registering-and-connecting-to-oracle-database"></a>Registrace a připojování k databázi Oracle
Při připojování ke zdrojům dat Oracle Database uživatelé mají nainstalovánu správné ovladače Oracle, které odpovídají bitové verze (32bitová nebo 64bitová verze) softwaru používán.

* Při registraci zdroje dat Oracle na počítači se systémem Windows 32-bit, použije se 32bitové ovladače Oracle
* Při registraci zdroje dat Oracle na počítači se systémem Windows 64-bit, použije se 64bitové ovladače Oracle
* Při připojování ke zdrojům dat Oracle pomocí aplikace Excel na počítači se systémem 32bitové verze systému Microsoft Office, včetně Windows 64-bit, bude použit 32bitové ovladače Oracle
* Při připojování ke zdrojům dat Oracle pomocí aplikace Excel na počítači se systémem 64bitová verze systému Microsoft Office, se použijí ovladače Oracle 64-bit

### <a name="registering-and-connecting-to-sql-server-reporting-services"></a>Registrace a připojování ke službě SQL Server Reporting Services
Podpora pro zdroje dat služby SQL Server Reporting Services (SSRS) je aktuálně omezená, v nativním režimu jen pro servery. Podpora pro služby SSRS v režimu serveru SharePoint bude přidána v novější verzi.

### <a name="opening-data-assets-in-excel"></a>Otevírání datových assetů v aplikaci Excel
Když otevřete datové assety v aplikaci Microsoft Excel z **Azure Data Catalog** portálu, uživatelům může zobrazit výzva s **upozornění zabezpečení Microsoft Excel** dialogové okno. Toto je standardní, očekávané chování a uživatele můžete vybrat **povolit** pokračujte.

Další informace najdete v tématu [zapnutí nebo vypnutí výstrah zabezpečení o odkazy a soubory z podezřelé weby](https://support.office.com/article/Enable-or-disable-security-alerts-about-links-and-files-from-suspicious-websites-A1AC6AE9-5C4A-4EB3-B3F8-143336039BBE).

### <a name="proxy-and-policy-configuration-and-data-source-registration"></a>Proxy server a zásad konfigurace a data registrace zdroje
Uživatelé mohou nastat situace, kdy se můžou přihlašovat na portál Azure Data Catalog, ale při pokusu o přihlášení k nástroj registrace zdroje dat narazí chybovou zprávu, která brání jejich protokolování.

Existují dva možné příčiny tohoto problému chování:

**1 příčina: Konfigurace služby AD FS** nástroj registrace zdroje dat používá ověřování založené na formulářích k ověření přihlášení uživatelů vůči službě Active Directory. Pro úspěšné přihlášení musí být povolené ověřování pomocí formulářů ve globální zásady ověřování microsoftem nebo správcem Active Directory.

V některých případech chování této chybě může dojít pouze v případě, že uživatel je v podnikové síti, nebo pouze v případě, že se uživatel připojuje mimo podnikovou síť. Globální zásady ověřování umožňuje metody ověřování pro připojení z extranetu a intranetu odděleně povolit. Pokud není povolené ověřování pomocí formulářů pro síť, ze kterého se uživatel připojuje, může dojít k chybám přihlášení.

Další informace najdete v tématu [konfigurace zásad ověřování](https://technet.microsoft.com/library/dn486781.aspx).

**Příčiny 2: Konfigurace proxy serveru síťových** Pokud podniková síť používá proxy server, nástroje pro registraci nemusí být schopná připojit ke službě Azure Active Directory přes proxy server. Uživatele můžete zajistit, že nástroj pro registraci tak, že upravíte konfigurační soubor nástroje, v této části přidáte do souboru:

      <system.net>
        <defaultProxy useDefaultCredentials="true" enabled="true">
          <proxy usesystemdefault="True"
                          proxyaddress="http://<your corporate network proxy url>"
                          bypassonlocal="True"/>
        </defaultProxy>
      </system.net>


Vyhledejte soubor RegistrationTool.exe.config, spustí se nástroj pro registraci a pak otevřete nástroj Správce úloh Windows. Na kartě Podrobnosti ve Správci úloh klikněte pravým tlačítkem na RegistrationTool.exe a zvolte Otevřít umístění souboru v místní nabídce.
