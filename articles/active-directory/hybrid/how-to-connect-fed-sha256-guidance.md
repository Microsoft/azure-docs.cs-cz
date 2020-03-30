---
title: Změna algoritmu hash podpisu pro důvěryhodný certifikát předávající strany Office 365 – Azure
description: Tato stránka obsahuje pokyny pro změnu algoritmu SHA pro federační důvěryhodnost s Office 365
keywords: SHA1,SHA256,O365,federation,aadconnect,adfs,ad fs,change sha,federation trust,relying party trust
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
ms.topic: conceptual
ms.date: 10/26/2018
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2233b434fda628dcf812a62f06541fc4b0296aba
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76897351"
---
# <a name="change-signature-hash-algorithm-for-office-365-relying-party-trust"></a>Změna algoritmu hash podpisu pro důvěryhodný certifikát předávající strany Office 365
## <a name="overview"></a>Přehled
Služba AD FS (Active Directory Federation Services) podepisuje své tokeny do služby Microsoft Azure Active Directory, aby bylo zajištěno, že s nimi nelze manipulovat. Tento podpis může být založen na SHA1 nebo SHA256. Azure Active Directory teď podporuje tokeny podepsané algoritmem SHA256 a doporučujeme nastavit algoritmus podepisování tokenů na SHA256 pro nejvyšší úroveň zabezpečení. Tento článek popisuje kroky potřebné k nastavení algoritmu podpisu tokenů na bezpečnější úroveň SHA256.

>[!NOTE]
>Společnost Microsoft doporučuje použití SHA256 jako algoritmus pro podepisování tokenů, protože je bezpečnější než SHA1, ale SHA1 stále zůstává podporovanou možností.

## <a name="change-the-token-signing-algorithm"></a>Změna algoritmu podpisu tokenu
Po nastavení algoritmu podpisu s jedním ze dvou procesů níže, AD FS podepíše tokeny pro Office 365 předávající strany důvěryhodnost s SHA256. Nemusíte provádět žádné další změny konfigurace a tato změna nemá žádný vliv na vaši schopnost přístupu k Office 365 nebo jiným aplikacím Azure AD.

### <a name="ad-fs-management-console"></a>Konzola pro správu služby AD FS
1. Otevřete konzolu pro správu služby AD FS na primárním serveru služby AD FS.
2. Rozbalte uzel ad FS a klepněte na **příkaz Vztahy důvěryhodnosti předávající strany**.
3. Klikněte pravým tlačítkem myši na důvěryhodnost důvěryhodnéstrany předávající office 365/Azure a vyberte **Vlastnosti**.
4. Vyberte kartu **Upřesnit** a vyberte algoritmus zabezpečeného algoritmu hash SHA256.
5. Klikněte na tlačítko **OK**.

![Algoritmus podepisování SHA256 – MMC](./media/how-to-connect-fed-sha256-guidance/mmc.png)

### <a name="ad-fs-powershell-cmdlets"></a>Rutiny prostředí PowerShell ve selužce AD FS
1. Na libovolném serveru služby AD FS otevřete prostředí PowerShell pod oprávněními správce.
2. Nastavte algoritmus hash zabezpečení pomocí rutiny **Set-AdfsRelyingPartyTrust.**
   
   <code>Set-AdfsRelyingPartyTrust -TargetName 'Microsoft Office 365 Identity Platform' -SignatureAlgorithm 'https://www.w3.org/2001/04/xmldsig-more#rsa-sha256'</code>

## <a name="also-read"></a>Přečtěte si také
* [Oprava vztahu důvěryhodnosti Office 365 pomocí Azure AD Connect](how-to-connect-fed-management.md#repairthetrust)

