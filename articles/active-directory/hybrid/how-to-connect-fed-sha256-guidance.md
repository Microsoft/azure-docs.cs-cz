---
title: Změnit algoritmus hash podpisu pro vztah důvěryhodnosti předávající strany Microsoft 365 – Azure
description: Tato stránka poskytuje pokyny pro změnu algoritmu SHA pro vztah důvěryhodnosti federace s Microsoft 365.
keywords: SHA1, SHA256, M365, federace, aadconnect, ADFS, AD FS, změna SHA, důvěryhodnost federace, vztah důvěryhodnosti předávající strany
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: cf6880e2-af78-4cc9-91bc-b64de4428bbd
ms.service: active-directory
ms.subservice: hybrid
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 10/26/2018
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6bf9347d4d14e6583febd4ffaf0447e912133b80
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "89660929"
---
# <a name="change-signature-hash-algorithm-for-microsoft-365-relying-party-trust"></a>Změnit algoritmus hash podpisu pro vztah důvěryhodnosti předávající strany Microsoft 365
## <a name="overview"></a>Přehled
Active Directory Federation Services (AD FS) (AD FS) podepisuje své tokeny do Microsoft Azure Active Directory, aby se zajistilo, že je nelze úmyslně poškodit. Tento podpis může být založený na SHA1 nebo SHA256. Azure Active Directory teď podporuje tokeny podepsané algoritmem SHA256 a doporučujeme nastavit algoritmus podepisování tokenů na SHA256 pro nejvyšší úroveň zabezpečení. Tento článek popisuje kroky potřebné k nastavení algoritmu podepisování tokenu na bezpečnější úroveň SHA256.

>[!NOTE]
>Microsoft doporučuje použití SHA256 jako algoritmu pro podepisování tokenů, protože je bezpečnější než SHA1, ale SHA1 stále zůstává podporovanou možností.

## <a name="change-the-token-signing-algorithm"></a>Změna podpisového algoritmu tokenů
Po nastavení algoritmu podpisu pomocí jednoho ze dvou procesů AD FS podepíše tokeny Microsoft 365 vztahu důvěryhodnosti předávající strany pomocí SHA256. Nemusíte provádět žádné dodatečné změny konfigurace a tato změna nemá žádný vliv na možnost přístupu k Microsoft 365 nebo jiným aplikacím služby Azure AD.

### <a name="ad-fs-management-console"></a>Konzola pro správu AD FS
1. Otevřete konzolu pro správu AD FS na primárním serveru AD FS.
2. Rozbalte uzel AD FS a klikněte na **vztahy důvěryhodnosti předávající strany**.
3. Klikněte pravým tlačítkem na Microsoft 365/vztah důvěryhodnosti předávající strany Azure a vyberte **vlastnosti**.
4. Vyberte kartu **Upřesnit** a vyberte zabezpečený algoritmus hash SHA256.
5. Klikněte na **OK**.

![SHA256 Signing Algorithm – MMC](./media/how-to-connect-fed-sha256-guidance/mmc.png)

### <a name="ad-fs-powershell-cmdlets"></a>Rutiny AD FS PowerShellu
1. Na jakémkoli serveru AD FS otevřete PowerShell v části oprávnění správce.
2. Pomocí rutiny **set-AdfsRelyingPartyTrust** nastavte algoritmus Secure Hash.
   
   <code>Set-AdfsRelyingPartyTrust -TargetName 'Microsoft Office 365 Identity Platform' -SignatureAlgorithm 'https://www.w3.org/2001/04/xmldsig-more#rsa-sha256'</code>

## <a name="also-read"></a>Přečíst také
* [Oprava Microsoft 365 důvěry pomocí Azure AD Connect](how-to-connect-fed-management.md#repairthetrust)

