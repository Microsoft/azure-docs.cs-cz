---
title: "Problém instalace agenta konektor Proxy aplikace | Microsoft Docs"
description: "Řešení potíží, která vám může při instalaci agenta konektor Proxy aplikace"
services: active-directory
documentationcenter: 
author: ajamess
manager: mtillman
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: asteen
ms.openlocfilehash: 8fbd707b6708661ab0d655afadff2b18694a981e
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/13/2018
---
# <a name="problem-installing-the-application-proxy-agent-connector"></a>Problém instalace agenta konektor Proxy aplikace

Microsoft AAD Application Proxy Connector je součást interní doméně, která používá odchozí připojení k navázání připojení z koncového bodu cloudu k dispozici k interní doméně.

## <a name="general-problem-areas-with-connector-installation"></a>Obecné problémových oblastí pomocí instalace konektoru

Při instalaci konektoru selže, hlavní příčinou je obvykle jednu z těchto oblastí:

1.  **Připojení** – dokončení úspěšné instalaci se nové konektor musí zaregistrovat a vytvořit vlastnosti budoucí vztah důvěryhodnosti. K tomu je potřeba připojení ke cloudové službě AAD Application Proxy.

2.  **Navázání vztahu důvěryhodnosti** – nový konektor vytvoří certifikátu podepsaného svým držitelem a zaregistruje do cloudové služby.

3.  **Ověřování správce** – během instalace, uživatel musí poskytnout přihlašovací údaje správce pro dokončení instalace konektoru.

## <a name="verify-connectivity-to-the-cloud-application-proxy-service-and-microsoft-login-page"></a>Ověření připojení k Proxy aplikace cloudové služby a Microsoft Login stránku

**Cíl:** ověřte, zda počítač konektoru může připojit k koncový bod registrace AAD aplikace Proxy, jakož i Microsoft přihlašovací stránku.

1.  Otevřete prohlížeč a přejděte na následující webové stránce: <https://aadap-portcheck.connectorporttest.msappproxy.net> a ověřte, zda je funkční připojení k datovým centrům střed USA a východní USA s porty 80 a 443.

2.  Pokud některé z těchto portů neproběhne úspěšně (nemá zeleného zaškrtnutí), ověřte, zda má proxy server brány Firewall nebo back-end \*. msappproxy.net s porty 80 a 443 správně definované.

3.  Otevřete prohlížeč (samostatné kartě) a přejděte na následující webové stránce: <https://login.microsoftonline.com>, ujistěte se, že se může přihlásit k této stránce.

## <a name="verify-machine-and-backend-components-support-for-application-proxy-trust-cert"></a>Ověřte, zda počítač a back-end součásti Podpora pro cert vztah důvěryhodnosti Proxy aplikace

**Cíl:** ověřte, zda počítač konektoru, back-end proxy a firewall podporuje certifikát vytvořený konektor pro budoucí vztah důvěryhodnosti.

>[!NOTE]
>Konektor se pokusí vytvořit SHA512 certifikátu, který podporuje TLS1.2. Pokud je počítač nebo back-end brány firewall a proxy server nepodporuje TLS1.2, instalace se nezdaří.
>
>

**K vyřešení problému:**

1.  Ověřte počítač podporuje TLS1.2 – verze všechny systémy Windows po 2012 R2 by měly podporovat protokol TLS 1.2. Pokud je počítač konektor z verze 2012 R2 nebo před, ujistěte se, že jsou na počítači nainstalovány následující články znalostní báze: <https://support.microsoft.com/help/2973337/sha512-is-disabled-in-windows-when-you-use-tls-1.2>

2.  Obraťte se na správce sítě a požádejte o ověření, že back-end proxy a firewall neblokují SHA512 pro odchozí provoz.

## <a name="verify-admin-is-used-to-install-the-connector"></a>Ověřte, že správce se používá k instalaci konektoru

**Cíl:** ověřte, že uživatel, který se snaží nainstalovat konektor je správce se správnými přihlašovacími údaji. V současné době uživatel musí být globálním správcem pro instalaci proběhla úspěšně.

**Chcete-li ověřit správnost přihlašovací údaje:**

Připojení k <https://login.microsoftonline.com> a používat stejné přihlašovací údaje. Ověřte, zda je přihlášení úspěšné. Role uživatele můžete zkontrolovat přechodem na **Azure Active Directory**  - &gt; **uživatelů a skupin**  - &gt; **všichni uživatelé**. 

Vyberte uživatelský účet, pak "Directory Role" v nabídce výsledné. Ověřte, zda vybranou roli "Globální správce". Pokud není možné získat přístup k libovolnému stránek společně tyto kroky, nejste globálním správcem.

## <a name="next-steps"></a>Další postup
[Pochopení konektory proxy aplikace služby Azure AD](application-proxy-understand-connectors.md)
