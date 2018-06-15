---
title: Algoritmus hash podpisu změnu vztahu důvěryhodnosti předávající strany Office 365 | Microsoft Docs
description: Tato stránka obsahuje pokyny pro změnu algoritmus SHA pro vztah důvěryhodnosti federace s Office 365
keywords: SHA1, SHA256, O365, federace, aadconnect, služby AD FS, služby ad fs, změny sha, vztah důvěryhodnosti federace, vztah důvěryhodnosti předávající strany
services: active-directory
documentationcenter: ''
author: anandyadavmsft
manager: mtillman
editor: ''
ms.assetid: cf6880e2-af78-4cc9-91bc-b64de4428bbd
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/31/2016
ms.author: anandy
ms.openlocfilehash: ec7eee36888d825d65335db590731103aabbf5c2
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/11/2017
ms.locfileid: "26598958"
---
# <a name="change-signature-hash-algorithm-for-office-365-relying-party-trust"></a>Změňte algoritmus hash podpisu pro Office 365 vztah důvěryhodnosti předávající strany
## <a name="overview"></a>Přehled
Active Directory Federation Services (AD FS) podepisuje jeho tokenů pro Microsoft Azure Active Directory pro zajištění, aby nemohlo být manipulováno s. Tento podpis může být založen na SHA1 nebo SHA256. Azure Active Directory teď podporuje tokeny, které jsou podepsané s algoritmem SHA256 a doporučujeme nastavení token podpisový algoritmus SHA256 pro nejvyšší úroveň zabezpečení. Tento článek popisuje kroky potřebné k nastavte token podpisový algoritmus SHA256 bezpečnější úroveň.

>[!NOTE]
>Společnost Microsoft doporučuje použití SHA256 jako algoritmu k podepisování tokenů, jako je bezpečnější než SHA1 ale SHA1 zůstává stále podporované možnosti.

## <a name="change-the-token-signing-algorithm"></a>Změna algoritmu podpisové
Po nastavení algoritmus podpisu s jedním z dva procesy níže, služby AD FS podepisuje tokeny pro Office 365 vztah důvěryhodnosti předávající strany s SHA256. Nepotřebujete žádné další konfigurace změny a tato změna nemá žádný vliv na schopnost přístupu k Office 365 nebo jiné aplikace Azure AD.

### <a name="ad-fs-management-console"></a>Konzola pro správu AD FS
1. Otevřete konzolu pro správu služby AD FS na primárním serveru služby AD FS.
2. Rozbalte uzel služby AD FS a klikněte na tlačítko **vztahy důvěryhodnosti předávající strany**.
3. Klikněte pravým tlačítkem na důvěryhodnosti předávající strany Office 365 nebo Azure a vyberte **vlastnosti**.
4. Vyberte **Upřesnit** a vyberte algoritmus zabezpečené hash SHA256.
5. Klikněte na **OK**.

![Podpisový algoritmus SHA256 – konzoly MMC](./media/active-directory-aadconnectfed-sha256guidance/mmc.png)

### <a name="ad-fs-powershell-cmdlets"></a>Rutiny služby AD FS prostředí PowerShell
1. Na všechny servery služby AD FS otevřete prostředí PowerShell v části oprávnění správce.
2. Nastavení algoritmu hash zabezpečené pomocí **Set-AdfsRelyingPartyTrust** rutiny.
   
   <code>Set-AdfsRelyingPartyTrust -TargetName 'Microsoft Office 365 Identity Platform' -SignatureAlgorithm 'http://www.w3.org/2001/04/xmldsig-more#rsa-sha256'</code>

## <a name="also-read"></a>Také pro čtení
* [Opravit vztah důvěryhodnosti Office 365 s Azure AD Connect](connect/active-directory-aadconnect-federation-management.md#repairthetrust)

