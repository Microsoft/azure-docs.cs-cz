---
title: Problém s instalací konektoru agenta proxy aplikace | Dokumenty společnosti Microsoft
description: Řešení problémů, kterým může dohlédnout při instalaci konektoru Agent proxy aplikace
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/21/2018
ms.author: mimart
ms.reviewer: japere
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4d773e6302edf0b799e6dfccc702750a9cc74f60
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81406685"
---
# <a name="problem-installing-the-application-proxy-agent-connector"></a>Potíže při instalaci konektoru agenta proxy aplikací

Microsoft AAD Application Proxy Connector je interní součást domény, která používá odchozí připojení k navázání připojení z cloudu dostupného koncového bodu k interní doméně.

## <a name="general-problem-areas-with-connector-installation"></a>Obecné problémové oblasti s instalací konektoru

Pokud instalace konektoru selže, hlavní příčinou je obvykle jedna z následujících oblastí:

1.  **Připojení** – k dokončení úspěšné instalace musí nový konektor zaregistrovat a vytvořit budoucí vlastnosti důvěryhodnosti. To se provádí připojením ke cloudové službě Proxy aplikace AAD.

2.  **Trust Establishment** – nový konektor vytvoří certifikát podepsaný svým držitelem a zaregistruje se do cloudové služby.

3.  **Ověřování správce** – během instalace musí uživatel zadat pověření správce k dokončení instalace konektoru.

> [!NOTE]
> Protokoly instalace konektoru naleznete ve složce %TEMP% a mohou poskytnout další informace o tom, co způsobuje selhání instalace.

## <a name="verify-connectivity-to-the-cloud-application-proxy-service-and-microsoft-login-page"></a>Ověření připojení ke službě Proxy aplikace cloudu a stránce Microsoft Login

**Cíl:** Ověřte, zda se počítač s konektory může připojit ke koncovému bodu registrace proxy aplikace AAD a přihlašovací stránce společnosti Microsoft.

1.  Na konektorovém serveru spusťte test portu pomocí [programu telnet](https://docs.microsoft.com/windows-server/administration/windows-commands/telnet) nebo jiného nástroje pro testování portů a ověřte, zda jsou porty 443 a 80 otevřené.

2.  Pokud některý z těchto portů není úspěšný, ověřte, zda brána firewall nebo proxy back-endu má přístup k požadovaným doménám a portům, viz [Příprava místního prostředí](application-proxy-add-on-premises-application.md#prepare-your-on-premises-environment).

3.  Otevřete prohlížeč (samostatnou kartu) a přejděte na následující webovou stránku: `https://login.microsoftonline.com`, ujistěte se, že se na tuto stránku můžete přihlásit.

## <a name="verify-machine-and-backend-components-support-for-application-proxy-trust-certificate"></a>Ověření podpory součástí počítače a back-endu pro certifikát důvěryhodnosti proxy aplikace

**Cíl:** Ověřte, zda počítač s konektorem, proxy server back-endu a brána firewall podporují certifikát vytvořený konektorem pro budoucí důvěryhodnost a zda je certifikát platný.

>[!NOTE]
>Spojnice se pokusí vytvořit certifikát SHA512, který je podporován TLS1.2. Pokud počítač nebo back-endfirewall a proxy server nepodporují TLS1.2, instalace se nezdaří.
>
>

**Zkontrolujte požadované požadavky:**

1.  Ověřte, zda počítač podporuje TLS1.2 – Všechny verze systému Windows po 2012 R2 by měly podporovat TLS 1.2. Pokud je váš konektor ový počítač z verze 2012 R2 nebo předchozí, ujistěte se, že jsou v počítači nainstalovány následující kb:<https://support.microsoft.com/help/2973337/sha512-is-disabled-in-windows-when-you-use-tls-1.2>

2.  Obraťte se na správce sítě a požádejte o ověření, zda back-endový proxy server a brána firewall neblokují sha512 pro odchozí provoz.

**Ověření klientského certifikátu:**

Ověřte kryptografický otisk aktuálního klientského certifikátu. Úložiště certifikátů naleznete v %ProgramData%\microsoft\Microsoft AAD Application Proxy Connector\Config\TrustSettings.xml

```
<?xml version="1.0" encoding="utf-8"?>
<ConnectorTrustSettingsFile xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  <CloudProxyTrust>
    <Thumbprint>4905CC64B2D81BBED60962ECC5DCF63F643CCD55</Thumbprint>
    <IsInUserStore>false</IsInUserStore>
  </CloudProxyTrust>
</ConnectorTrustSettingsFile>
```

Zde jsou možné **isinuserstore** hodnoty a významy:

- **false** - Klientský certifikát byl vytvořen během instalace nebo registrace iniciované příkazem Register-AppProxyConnector. Je uložen v osobním kontejneru v úložišti certifikátů místního počítače. 

Certifikát ověřte takto:

1. Spustit **certlm.msc**
2. V konzole pro správu rozbalte osobní kontejner a klikněte na certifikáty
3. Vyhledejte certifikát vydaný **connectorregistrationca.msappproxy.net**

- **true** – Automaticky obnovený certifikát je uložen v osobním kontejneru v úložišti uživatelských certifikátů síťové služby. 

Certifikát ověřte takto:

1. Stažení [PsTools.zip](https://docs.microsoft.com/sysinternals/downloads/pstools)
2. Extrahujte [psexec](https://docs.microsoft.com/sysinternals/downloads/psexec) z balíčku a spusťte **psexec -i -u "nt authority\network service" cmd.exe** z příkazového řádku se zvýšenými oprávněními.
3. Spustit **certmgr.msc** v nově objeveném příkazovém řádku
2. V konzole pro správu rozbalte osobní kontejner a klikněte na certifikáty
3. Vyhledejte certifikát vydaný **connectorregistrationca.msappproxy.ne

**Obnovení klientského certifikátu:**

Pokud konektor není připojen ke službě po dobu několika měsíců, jeho certifikáty mohou být zastaralé. Selhání obnovení certifikátu vede k certifikátu, jehož platnost vypršela. Díky tomu služba konektoru přestane fungovat. Událost 1000 je zaznamenána v admin protokolu konektoru:

"Obnovení registrace konektoru se nezdařilo: Platnost certifikátu důvěryhodnosti konektoru vypršela. Spusťte rutinu PowerShell Register-AppProxyConnector v počítači, ve kterém je spuštěn konektor, abyste znovu zaregistrovali konektor."

V takovém případě odinstalujte a znovu nainstalujte konektor pro aktivaci registrace nebo můžete spustit následující příkazy prostředí PowerShell:

```
Import-module AppProxyPSModule
Register-AppProxyConnector
```

Další informace o příkazu Register-AppProxyConnector najdete v [tématu Vytvoření skriptu bezobslužné instalace pro konektor proxy aplikací Azure AD](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-register-connector-powershell)

## <a name="verify-admin-is-used-to-install-the-connector"></a>Ověření, že správce slouží k instalaci konektoru

**Cíl:** Ověřte, zda je uživatel, který se pokusí nainstalovat konektor, správcem se správnými pověřeními. V současné době musí být uživatel alespoň správcem aplikace, aby instalace byla úspěšná.

**Chcete-li ověřit, že pověření jsou správná:**

Připojte `https://login.microsoftonline.com` se ke stejným přihlašovacím údajům a použijte je. Ujistěte se, že přihlášení je úspěšné. Roli uživatele můžete zkontrolovat tak, že přejdete na Azure **Active Directory**  - &gt; **Users and Groups**  - &gt; **All Users**. 

Vyberte svůj uživatelský účet a pak "Role adresáře" ve výsledné nabídce. Ověřte, zda je vybraná role "Správce aplikace". Pokud se vám nedaří získat přístup k žádné ze stránek v těchto krocích, nemáte požadovanou roli.

## <a name="next-steps"></a>Další kroky
[Principy konektorů proxy aplikací Azure AD](application-proxy-connectors.md)
