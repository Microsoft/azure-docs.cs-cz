---
title: Problému s instalací konektorem agenta aplikačního proxy serveru | Dokumentace Microsoftu
description: Jak řešit problémy, se kterými může setkat při instalaci konektorem agenta aplikačního proxy serveru
services: active-directory
documentationcenter: ''
author: barbkess
manager: daveba
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/21/2018
ms.author: barbkess
ms.reviewer: japere
ms.openlocfilehash: c383bde930f9de21b3f2a532ef3f440e72445ddc
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2019
ms.locfileid: "55164499"
---
# <a name="problem-installing-the-application-proxy-agent-connector"></a>Potíže při instalaci konektoru agenta proxy aplikací

Microsoft AAD Application Proxy Connector je součást interní domény, který používá odchozí připojení k navázání připojení k interní doméně z koncového bodu cloudu k dispozici.

## <a name="general-problem-areas-with-connector-installation"></a>Obecné problémových oblastí pomocí instalace konektoru

Při instalaci konektoru selže, hlavní příčinou je obvykle jednu z následujících oblastí:

1.  **Připojení** – dokončení úspěšné instalaci nového konektoru potřeba zaregistrovat a vytvořit vlastnosti budoucí důvěryhodnosti. To se provádí pomocí připojení ke cloudové službě AAD Application Proxy.

2.  **Navázání vztahu důvěryhodnosti** – nový konektor vytvoří certifikátu podepsaného svým držitelem a zaregistruje ke cloudové službě.

3.  **Ověřování správu** – během instalace, musí uživatel zadat přihlašovací údaje správce pro dokončení instalace konektoru.

## <a name="verify-connectivity-to-the-cloud-application-proxy-service-and-microsoft-login-page"></a>Ověření připojení k Proxy aplikace Cloud service a Microsoft Login stránku

**Cíl:** Ověřte, že konektor počítače může připojit k koncový bod registrace AAD Application Proxy, stejně jako přihlašovací stránku Microsoft.

1.  Otevřete prohlížeč a přejděte na webové stránce: <https://aadap-portcheck.connectorporttest.msappproxy.net> a ověřte, zda je funkční připojení k datovým centrům střed USA a východní USA se porty 80 a 443.

2.  Pokud některý z těchto portů se nepovedlo úspěšně dokončit (nemá zelenou značku zaškrtnutí), ověřte, zda má proxy server brány Firewall nebo back-endu \*. msappproxy.net se porty 80 a 443 správně definován.

3.  Otevřete prohlížeč (samostatné kartě) a přejděte na webové stránce: <https://login.microsoftonline.com>, ujistěte se, že se může přihlásit k této stránce.

## <a name="verify-machine-and-backend-components-support-for-application-proxy-trust-cert"></a>Ověřte, zda počítač a back-end komponentami podporu certifikátu vztahu důvěryhodnosti Proxy aplikací

**Cíl:** Ověřte, zda počítač konektoru, back-endu proxy a brána firewall podporuje certifikát vytvořený konektor pro budoucí vztah důvěryhodnosti.

>[!NOTE]
>Konektor se pokusí vytvořit SHA512 certifikát, který podporuje TLS1.2. Pokud je počítač nebo back-end bránu firewall a proxy server nepodporuje TLS1.2, instalace se nezdaří.
>
>

**Řešení tohoto problému:**

1.  Ověřte počítač podporuje TLS1.2 – všechny Windows verze po 2012 R2 by měly podporovat protokol TLS 1.2. Pokud je váš počítač konektor z verze 2012 R2 nebo před, ujistěte se, že následující články znalostní báze jsou nainstalované na počítači: <https://support.microsoft.com/help/2973337/sha512-is-disabled-in-windows-when-you-use-tls-1.2>

2.  Obraťte se na správce sítě a požádat o ověření, že back-endu proxy a firewallu neblokují SHA512 pro odchozí provoz.

## <a name="verify-admin-is-used-to-install-the-connector"></a>Ověřte, že správce se používá k instalaci konektoru

**Cíl:** Ověřte, že uživatel, který se snaží nainstalovat konektor je správce se správnými přihlašovacími údaji. V současné době uživatel musí být buď správce aplikace nebo globální správce pro instalace proběhla úspěšně.

**Chcete-li ověřit, že jsou správné přihlašovací údaje:**

Připojte se k <https://login.microsoftonline.com> a použijte stejné přihlašovací údaje. Ujistěte se, že je přihlášení úspěšné. Role uživatele můžete zkontrolovat tak, že přejdete do **Azure Active Directory**  - &gt; **uživatelů a skupin**  - &gt; **všichni uživatelé**. 

Vyberte uživatelský účet, pak "Role adresáře" v nabídce výsledný. Ověřte, že vybranou roli "aplikace" globální správce"nebo". Pokud nemůžete získat přístup k žádnému stránek podél těchto kroků, nemáte požadované role.

## <a name="next-steps"></a>Další postup
[Principy konektorů Proxy aplikací Azure AD](application-proxy-connectors.md)
