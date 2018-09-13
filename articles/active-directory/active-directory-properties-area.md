---
title: Přidejte informace o ochraně osobních údajů vaší organizace ve službě Azure AD | Dokumentace Microsoftu
description: Vysvětluje, jak přidat informace o ochraně osobních údajů vaší organizace do oblasti Vlastnosti služby Azure Active Directory (Azure AD).
services: active-directory
documentationcenter: ''
author: eross-msft
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/17/2018
ms.author: lizross
ms.reviewer: bpham
ms.custom: it-pro
ms.openlocfilehash: a34fa2b8c2d966af108664c219a222fb9a5b7abc
ms.sourcegitcommit: e8f443ac09eaa6ef1d56a60cd6ac7d351d9271b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/12/2018
ms.locfileid: "35899143"
---
# <a name="how-to-add-your-organizations-privacy-info-in-azure-active-directory"></a>Postupy: Přidání informací o ochraně osobních údajů vaší organizace v Azure Active Directory
Tento článek vysvětluje, jak může správce tenanta přidat informace týkající se ochrany osobních údajů v organizaci tenanta Azure Active Directory (Azure AD), na webu Azure portal.

Důrazně doporučujeme že přidat kontakt globální ochrany osobních údajů a zásady ochrany osobních údajů vaší organizace, tak interní zaměstnance a externí hosté můžou, projděte si zásady. Protože prohlášení o ochraně osobních údajů jsou jednoznačně vytvořen a přizpůsobené pro každou firmu, důrazně doporučujeme, abyste že požádali advokát žádostí o pomoc.

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-dsr-and-stp-note.md)]

## <a name="access-the-properties-area-to-add-your-privacy-info"></a>Přístup k vlastnosti oblasti, ke které přidejte své informace o ochraně osobních údajů

1.  Přihlaste se k webu Azure portal jako správce tenanta.

2.  V levém navigačním panelu vyberte **Azure Active Directory**a pak vyberte **vlastnosti**.

    **Vlastnosti** se zobrazí oblast.

    ![Zvýraznění oblasti informace o ochraně osobních údajů Azure AD vlastnosti oblasti](./media/active-directory-properties-area/properties-area.png)

3.  Přidejte své informace o ochraně osobních údajů pro svoje zaměstnance:

    - **Technický kontakt.** Zadejte e-mailovou adresu osoby, obraťte se na technickou podporu v rámci vaší organizace.
    
    - **Obraťte se na globální ochrany osobních údajů.** Zadejte e-mailovou adresu pro osobu požádat o dotazy týkající se ochrany osobních údajů osobní údaje. Tato osoba je také kdo Microsoft obrátí, pokud dojde k porušení zabezpečení dat. Pokud neexistuje žádné osobě, tady najdete, kontaktuje Microsoft globální správci.

    - **Adresa URL prohlášení o ochraně osobních údajů.** Zadejte odkaz na dokument vaší organizace, popisující, jak organizace zpracovává i interních a externích hosta ochrany osobních údajů.

        >[!Important]
        >Pokud nechcete zahrnout vlastní prohlášení o ochraně osobních údajů nebo ochranu osobních údajů kontaktu, externí hosté se zobrazí text **kontrola oprávnění** pole,  **< _název vaší organizace_> neposkytl odkazy na podmínkách ke kontrole**. Při přijetí pozvánky pro přístup k organizaci prostřednictvím spolupráce B2B ve službě například uživatel typu Host se zobrazí tato zpráva.

        ![Oprávnění revize spolupráce B2B pole se zprávou](./media/active-directory-properties-area/active-directory-no-privacy-statement-or-contact.png)

4.  Vyberte **Uložit**.

## <a name="next-steps"></a>Další postup
- [Uplatnění pozvání spolupráce služby Azure Active Directory s B2B](https://aka.ms/b2bredemption)
- [Přidání nebo změně informací profilu pro uživatele v Azure Active Directory](fundamentals/active-directory-users-profile-azure-portal.md)