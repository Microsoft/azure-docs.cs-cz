---
title: Při instalaci konektoru agenta aplikačního proxy serveru došlo k problému | Microsoft Docs
description: Jak řešit problémy, které se můžou při instalaci konektoru agenta proxy aplikací nainstalovat
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
ms.openlocfilehash: a35558b81d064680981bcf403a3584e3a3d00e4f
ms.sourcegitcommit: 9dec0358e5da3ceb0d0e9e234615456c850550f6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/14/2019
ms.locfileid: "72311745"
---
# <a name="problem-installing-the-application-proxy-agent-connector"></a>Potíže při instalaci konektoru agenta proxy aplikací

Microsoft AAD Application proxy Connector je interní doménová komponenta, která používá odchozí připojení k navázání připojení z koncového bodu dostupného z cloudu do interní domény.

## <a name="general-problem-areas-with-connector-installation"></a>Obecné oblasti problému s instalací konektoru

V případě selhání instalace konektoru je hlavní příčinou obvykle jedna z následujících oblastí:

1.  **Připojení** – aby bylo možné dokončit úspěšnou instalaci, musí nový konektor registrovat a navázat budoucí vlastnosti důvěryhodnosti. To se provádí připojením ke cloudové službě aplikace AAD proxy.

2.  Vystavení **důvěryhodnosti** – nový konektor vytvoří certifikát podepsaný svým držitelem a zaregistruje se do cloudové služby.

3.  Při **ověřování správce** – během instalace musí uživatel zadat přihlašovací údaje správce, aby bylo možné dokončit instalaci konektoru.

> [!NOTE]
> Protokoly instalace konektoru najdete ve složce% TEMP% a můžou vám poskytnout další informace o tom, co způsobuje selhání instalace.

## <a name="verify-connectivity-to-the-cloud-application-proxy-service-and-microsoft-login-page"></a>Ověření připojení ke službě cloud Application proxy a přihlašovací stránce Microsoftu

**Cíl:** Ověřte, že se počítač konektoru může připojit ke koncovému bodu registrace proxy aplikace AAD i k přihlašovací stránce Microsoftu.

1.  Na serveru konektoru spusťte test portu pomocí [protokolu Telnet](https://docs.microsoft.com/windows-server/administration/windows-commands/telnet) nebo jiného nástroje pro testování portů, abyste ověřili, že jsou otevřené porty 443 a 80.

2.  Pokud některý z těchto portů neproběhne úspěšně, ověřte, zda má server firewall nebo back-end proxy přístup k požadovaným doménám a portům v tématu [Příprava místního prostředí](application-proxy-add-on-premises-application.md#prepare-your-on-premises-environment).

3.  Otevřete prohlížeč (oddělený tabulátorem) a přejít na následující webovou stránku: <https://login.microsoftonline.com>, ujistěte se, že se k této stránce můžete přihlásit.

## <a name="verify-machine-and-backend-components-support-for-application-proxy-trust-cert"></a>Ověření podpory součástí počítačů a back-endu pro certifikát důvěryhodnosti proxy aplikací

**Cíl:** Ověřte, že počítač konektoru, back-end proxy serveru a firewall může podporovat certifikát vytvořený konektorem pro budoucí důvěryhodnost.

>[!NOTE]
>Konektor se pokusí vytvořit SHA512 certifikát podporovaný protokolem TLS 1.2. Pokud počítač nebo back-end firewall a proxy server nepodporují protokol TLS 1.2, instalace se nezdařila.
>
>

**Problém vyřešíte takto:**

1.  Ověřte, že počítač podporuje TLS 1.2 – všechny verze Windows po 2012 R2 by měly podporovat TLS 1,2. Pokud je váš počítač konektoru z verze 2012 R2 nebo starší, ujistěte se, že je na počítači nainstalovaný následující aktualizací KB: <https://support.microsoft.com/help/2973337/sha512-is-disabled-in-windows-when-you-use-tls-1.2>

2.  Obraťte se na správce sítě a požádejte ho o ověření, že back-end proxy serveru a brána firewall neblokují SHA512 pro odchozí provoz.

## <a name="verify-admin-is-used-to-install-the-connector"></a>Ověření, že se k instalaci konektoru používá správce

**Cíl:** Ověřte, že uživatel, který se pokouší nainstalovat konektor, je správcem se správnými přihlašovacími údaji. V současné době musí být uživatel alespoň správcem aplikace, aby instalace proběhla úspěšně.

**Ověření správnosti přihlašovacích údajů:**

Připojte se k <https://login.microsoftonline.com> a používejte stejné přihlašovací údaje. Ujistěte se, že přihlášení bylo úspěšné. Roli uživatele můžete ověřit tak, že na **Azure Active Directory** -&gt; **uživatelé a skupiny** -&gt; **Všichni uživatelé**. 

V výsledné nabídce vyberte svůj uživatelský účet a potom zadejte "role adresáře". Ověřte, jestli je vybraná role Správce aplikací. Pokud nemůžete získat přístup k žádné ze stránek tohoto postupu, nemáte požadovanou roli.

## <a name="next-steps"></a>Další kroky
[Principy konektorů Proxy aplikací Azure AD](application-proxy-connectors.md)
