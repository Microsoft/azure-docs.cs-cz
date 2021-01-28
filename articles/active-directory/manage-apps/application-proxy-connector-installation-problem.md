---
title: Potíže při instalaci konektoru agenta proxy aplikací
description: Jak řešit problémy, na které se můžete setkat při instalaci konektoru agenta proxy aplikací pro Azure Active Directory.
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: troubleshooting
ms.date: 01/28/2021
ms.author: kenwith
ms.reviewer: japere
ms.openlocfilehash: 04c26609b046f7525c513796622be74633a20e91
ms.sourcegitcommit: 04297f0706b200af15d6d97bc6fc47788785950f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/28/2021
ms.locfileid: "98986764"
---
# <a name="problem-installing-the-application-proxy-agent-connector"></a>Potíže při instalaci konektoru agenta proxy aplikací

Microsoft Azure Active Directory konektor proxy aplikací je interní doménová komponenta, která používá odchozí připojení k navázání připojení z koncového bodu dostupného v cloudu k interní doméně.

## <a name="general-problem-areas-with-connector-installation"></a>Obecné oblasti problému s instalací konektoru

V případě selhání instalace konektoru je hlavní příčinou obvykle jedna z následujících oblastí. **Jako ukazatel na případné řešení potíží nezapomeňte konektor restartovat.**

1.  **Připojení** – aby bylo možné dokončit úspěšnou instalaci, musí nový konektor registrovat a navázat budoucí vlastnosti důvěryhodnosti. To se provádí připojením ke cloudové službě Proxy aplikací služby Azure Active Directory.

2.  Vystavení **důvěryhodnosti** – nový konektor vytvoří certifikát podepsaný svým držitelem a zaregistruje se do cloudové služby.

3.  Při **ověřování správce** – během instalace musí uživatel zadat přihlašovací údaje správce, aby bylo možné dokončit instalaci konektoru.

> [!NOTE]
> Protokoly instalace konektoru najdete ve složce% TEMP% a můžou vám poskytnout další informace o tom, co způsobuje selhání instalace.

## <a name="verify-connectivity-to-the-cloud-application-proxy-service-and-microsoft-login-page"></a>Ověření připojení ke službě cloud Application proxy a přihlašovací stránce Microsoftu

**Cíl:** Ověřte, že se počítač konektoru může připojit ke koncovému bodu registrace proxy aplikace a také k přihlašovací stránce Microsoftu.

1.  Na serveru konektoru spusťte test portu pomocí [protokolu Telnet](/windows-server/administration/windows-commands/telnet) nebo jiného nástroje pro testování portů, abyste ověřili, že jsou otevřené porty 443 a 80.

2.  Pokud některý z těchto portů neproběhne úspěšně, ověřte, zda má server firewall nebo back-end proxy přístup k požadovaným doménám a portům v tématu [Příprava místního prostředí](application-proxy-add-on-premises-application.md#prepare-your-on-premises-environment).

3.  Otevřete prohlížeč (oddělený tabulátorem) a přejít na následující webovou stránku: Ujistěte se, `https://login.microsoftonline.com` že se k této stránce můžete přihlásit.

## <a name="verify-machine-and-backend-components-support-for-application-proxy-trust-certificate"></a>Ověření podpory součástí počítačů a back-endu pro certifikát důvěryhodnosti proxy aplikací

**Cíl:** Ověřte, že počítač konektoru, back-end proxy server a firewall můžou podporovat certifikát vytvořený konektorem pro budoucí důvěryhodnost a že certifikát je platný.

>[!NOTE]
>Konektor se pokusí vytvořit SHA512 certifikát podporovaný protokolem TLS 1.2. Pokud počítač nebo back-end firewall a proxy server nepodporují protokol TLS 1.2, instalace se nezdařila.
>
>

**Kontrola požadovaných požadavků:**

1.  Ověřte, že počítač podporuje TLS 1.2 – všechny verze Windows po 2012 R2 by měly podporovat TLS 1,2. Pokud je váš počítač konektoru z verze 2012 R2 nebo starší, ujistěte se, že je na počítači nainstalovaný následující aktualizací KB: <https://support.microsoft.com/help/2973337/sha512-is-disabled-in-windows-when-you-use-tls-1.2>

2.  Obraťte se na správce sítě a požádejte ho o ověření, že back-end proxy serveru a brána firewall neblokují SHA512 pro odchozí provoz.

**Ověření klientského certifikátu:**

Ověřte kryptografický otisk aktuálního certifikátu klienta. Úložiště certifikátů najdete v části `%ProgramData%\microsoft\Microsoft AAD Application Proxy Connector\Config\TrustSettings.xml` .

```
<?xml version="1.0" encoding="utf-8"?>
<ConnectorTrustSettingsFile xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  <CloudProxyTrust>
    <Thumbprint>4905CC64B2D81BBED60962ECC5DCF63F643CCD55</Thumbprint>
    <IsInUserStore>false</IsInUserStore>
  </CloudProxyTrust>
</ConnectorTrustSettingsFile>
```

Možné hodnoty **IsInUserStore** jsou **true** a **false**. Hodnota **true** znamená, že se automaticky obnovený certifikát uloží do osobního kontejneru v úložišti certifikátů uživatele síťové služby. Hodnota **false** znamená, že klientský certifikát byl vytvořen během instalace nebo registrace iniciované příkazem Register-AppProxyConnector a je uložen v osobním kontejneru v úložišti certifikátů místního počítače.

Pokud je hodnota **true**, ověřte certifikát pomocí těchto kroků:
1. Stáhnout [PsTools.zip](/sysinternals/downloads/pstools)
2. Z příkazového řádku se zvýšenými oprávněními rozbalte [PsExec](/sysinternals/downloads/psexec) z balíčku a spusťte **PsExec-i-u "NT authority\network Service" cmd.exe** .
3. Spusťte **certmgr. msc** na nově zobrazeném příkazovém řádku.
4. V konzole pro správu rozbalte osobní kontejner a klikněte na certifikáty.
5. Vyhledejte certifikát vydaný pomocí **connectorregistrationca.msappproxy.NET**

Pokud je hodnota **false**, ověřte certifikát pomocí těchto kroků:
1. Spusťte **Certlm. msc.**
2. V konzole pro správu rozbalte osobní kontejner a klikněte na certifikáty.
3. Vyhledejte certifikát vydaný pomocí **connectorregistrationca.msappproxy.NET**

**Postup obnovení klientského certifikátu:**

Pokud konektor není po několik měsíců připojený ke službě, můžou být jeho certifikáty zastaralé. Selhání obnovení certifikátu vede k certifikátu, jehož platnost vypršela. Díky tomu služba konektoru přestane fungovat. Událost 1000 se zaznamená do protokolu pro správu konektoru:

"Opětovné registrace konektoru se nezdařila: vypršela platnost certifikátu vztahu důvěryhodnosti konektoru. Spusťte Register-AppProxyConnector rutiny prostředí PowerShell v počítači, ve kterém je konektor spuštěný, aby se váš konektor znovu zaregistroval. "

V takovém případě odinstalujte a znovu nainstalujte konektor, aby se aktivovala registrace, nebo můžete spustit následující příkazy PowerShellu:

```
Import-module AppProxyPSModule
Register-AppProxyConnector
```

Další informace o příkazu Register-AppProxyConnector najdete v tématu [Vytvoření skriptu bezobslužné instalace pro konektor Azure proxy aplikací služby AD](./application-proxy-register-connector-powershell.md) .

## <a name="verify-admin-is-used-to-install-the-connector"></a>Ověření, že se k instalaci konektoru používá správce

**Cíl:** Ověřte, že uživatel, který se pokouší nainstalovat konektor, je správcem se správnými přihlašovacími údaji. V současné době musí být uživatel alespoň správcem aplikace, aby instalace proběhla úspěšně.

**Ověření správnosti přihlašovacích údajů:**

Připojte se `https://login.microsoftonline.com` a použijte stejné přihlašovací údaje. Ujistěte se, že přihlášení bylo úspěšné. Roli uživatele můžete ověřit tak, že v části **Azure Active Directory**  - &gt; **Uživatelé a skupiny**  - &gt; **Všichni uživatelé**. 

V výsledné nabídce vyberte svůj uživatelský účet a potom zadejte "role adresáře". Ověřte, jestli je vybraná role Správce aplikací. Pokud nemůžete získat přístup k žádné ze stránek tohoto postupu, nemáte požadovanou roli.

## <a name="next-steps"></a>Další kroky
[Vysvětlení konektorů Azure Proxy aplikací služby AD](application-proxy-connectors.md)
