---
title: Změna podpisového hashovacího algoritmu pro Office 365 vztah důvěryhodnosti předávající strany | Dokumentace Microsoftu
description: Tato stránka poskytuje pokyny pro změnu algoritmus SHA pro vztah důvěryhodnosti federace s Office 365
keywords: SHA1, SHA256, O365, federace, aadconnect, služby AD FS, ad fs, změny sha důvěryhodnosti federace, vztah důvěryhodnosti předávající strany
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: cf6880e2-af78-4cc9-91bc-b64de4428bbd
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/26/2018
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9aa597c8b458305946aa298631726df3da317534
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/13/2019
ms.locfileid: "56181367"
---
# <a name="change-signature-hash-algorithm-for-office-365-relying-party-trust"></a>Změna podpisového hashovacího algoritmu pro Office 365 vztah důvěryhodnosti předávající strany
## <a name="overview"></a>Přehled
Active Directory Federation Services (AD FS) podepíše jeho tokeny do Microsoft Azure Active Directory a ujistěte se, že nemohlo být manipulováno s. Tento podpis může být založen na SHA1 nebo SHA256. Azure Active Directory teď podporuje tokeny podepsána s algoritmem SHA256. proto doporučujeme nastavit podpisové algoritmus SHA256 pro nejvyšší úroveň zabezpečení. Tento článek popisuje kroky potřebné k nastavení podpisové algoritmus SHA256 bezpečnější úrovně.

>[!NOTE]
>Společnost Microsoft doporučuje použití SHA256 jako algoritmus k podepisování tokenů, jako je bezpečnější než SHA1, ale stále zůstává podporovanou možností SHA1.

## <a name="change-the-token-signing-algorithm"></a>Změňte algoritmus podepisování
Po nastavení algoritmus podpisu jedním ze dvou procesy uvedené níže služby AD FS podepisuje tokeny pro Office 365 vztah důvěryhodnosti předávající strany pomocí SHA256. Není nutné provádět žádné další konfigurace změny a tato změna nemá žádný vliv na schopnost přístupu k Office 365 nebo jiným aplikacím služby Azure AD.

### <a name="ad-fs-management-console"></a>Konzola pro správu AD FS
1. Otevřete konzolu pro správu služby AD FS na primárním serveru služby AD FS.
2. Rozbalte uzel služby AD FS a klikněte na tlačítko **vztahy důvěryhodnosti předávající strany**.
3. Klikněte pravým tlačítkem na důvěryhodnosti předávající strany Office 365 nebo Azure a vyberte **vlastnosti**.
4. Vyberte **Upřesnit** kartě a vyberte zabezpečené hashovací algoritmus SHA256.
5. Klikněte na **OK**.

![Podpisový algoritmus SHA256 – konzoly MMC](./media/how-to-connect-fed-sha256-guidance/mmc.png)

### <a name="ad-fs-powershell-cmdlets"></a>Rutiny služby AD FS Powershellu
1. Na libovolném serveru služby AD FS otevřete prostředí PowerShell v rámci oprávnění správce.
2. Nastavení zabezpečení hashovacího algoritmu použít **Set-AdfsRelyingPartyTrust** rutiny.
   
   <code>Set-AdfsRelyingPartyTrust -TargetName 'Microsoft Office 365 Identity Platform' -SignatureAlgorithm 'https://www.w3.org/2001/04/xmldsig-more#rsa-sha256'</code>

## <a name="also-read"></a>Také číst
* [Oprava důvěryhodnosti služeb Office 365 s Azure AD Connect](how-to-connect-fed-management.md#repairthetrust)

