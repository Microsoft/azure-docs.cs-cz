---
title: Přidat informace o ochraně osobních údajů vaší organizace – Azure Active Directory | Microsoft Docs
description: Pokyny k přidání informací o ochraně osobních údajů vaší organizace do oblasti vlastností Azure Active Directory.
services: active-directory
author: ajburnle
manager: daveba
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: how-to
ms.date: 04/17/2018
ms.author: ajburnle
ms.reviewer: bpham
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: f47ae9b087615a77f6bd4d3f14f1b0052037cba6
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "95996746"
---
# <a name="add-your-organizations-privacy-info-using-azure-active-directory"></a>Přidání informací o ochraně osobních údajů vaší organizace pomocí Azure Active Directory
Tento článek vysvětluje, jak může správce klienta přidávat informace týkající se ochrany osobních údajů do tenanta Azure Active Directory organizace (Azure AD) prostřednictvím Azure Portal.

Důrazně doporučujeme přidat globální kontakt na soukromí a prohlášení o zásadách ochrany osobních údajů vaší organizace, aby vaši interní zaměstnanci a externí hosté mohli zkontrolovat vaše zásady. Vzhledem k tomu, že jsou prohlášení o zásadách ochrany osobních údajů jednoznačně vytvořená a přizpůsobená pro každou firmu, důrazně doporučujeme, abyste se obrátili na radu

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-dsr-and-stp-note.md)]

## <a name="add-your-privacy-info-on-azure-ad"></a>Přidání informací o ochraně osobních údajů v Azure AD
Do oblasti **vlastnosti** Azure AD přidáte informace o ochraně osobních údajů vaší organizace.

### <a name="to-access-the-properties-area-and-add-your-privacy-information"></a>Přístup k oblasti vlastnosti a přidání informací o ochraně osobních údajů

1. Přihlaste se k Azure Portal jako správce tenanta.

2. V levém navigačním panelu vyberte **Azure Active Directory** a pak vyberte **vlastnosti**.

    Zobrazí se oblast **vlastnosti** .

    ![Oblast vlastností Azure AD – zvýraznění oblasti informací o ochraně osobních údajů](media/active-directory-properties-area/properties-area.png)

3. Přidejte své informace o ochraně osobních údajů pro své zaměstnance:

    - **Technický kontakt.** Zadejte e-mailovou adresu osoby, která má požádat o technickou podporu v rámci vaší organizace.
    
    - **Globální kontakt na soukromí.** Zadejte e-mailovou adresu osoby, která se obrátí na dotazy týkající se ochrany osobních údajů osobních údajů. Tato osoba také v případě, že dojde k porušení dat, kontaktuje společnost Microsoft. Pokud zde není uvedená žádná osoba, společnost Microsoft bude kontaktovat vaše globální správce.

    - **Adresa URL prohlášení o ochraně osobních údajů** Zadejte odkaz na dokument vaší organizace, který popisuje, jak vaše organizace zpracovává soukromí dat interního i externího hosta.

        >[!Important]
        >Pokud nezahrnete vlastní prohlášení o zásadách ochrany osobních údajů ani váš kontakt na ochranu osobních údajů, vaše externí hosté uvidí text v poli pro **kontrolu oprávnění** , která uvádí, že **< _název vaší organizace_> neposkytl odkazy na své výrazy, abyste si je mohli prohlédnout**. Uživatel typu Host uvidí například tuto zprávu, když obdrží pozvánku k přístupu k organizaci prostřednictvím spolupráce B2B.

        ![Pole oprávnění pro kontrolu spolupráce B2B se zprávou](media/active-directory-properties-area/active-directory-no-privacy-statement-or-contact.png)

4. Vyberte **Uložit**.

## <a name="next-steps"></a>Další kroky
- [Uplatnění pozvánky ke spolupráci B2B v Azure Active Directory](../external-identities/redemption-experience.md)
- [Přidání nebo změna informací o profilu uživatele v Azure Active Directory](active-directory-users-profile-azure-portal.md)