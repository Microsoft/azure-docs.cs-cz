---
title: Přidání informací o ochraně osobních údajů vaší organizace – Azure Active Directory | Dokumentace Microsoftu
description: Pokyny ohledně toho, jak přidat informace o ochraně osobních údajů vaší organizace do oblasti Vlastnosti služby Azure Active Directory.
services: active-directory
author: eross-msft
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/17/2018
ms.author: lizross
ms.reviewer: bpham
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 98b4e3585f25a0b081e30c5524678a0f7bb27942
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/13/2019
ms.locfileid: "56187266"
---
# <a name="add-your-organizations-privacy-info-using-azure-active-directory"></a>Přidání informací o ochraně osobních údajů vaší organizace pomocí služby Azure Active Directory
Tento článek vysvětluje, jak může správce tenanta přidat informace týkající se ochrany osobních údajů v organizaci tenanta Azure Active Directory (Azure AD), na webu Azure portal.

Důrazně doporučujeme že přidat kontakt globální ochrany osobních údajů a zásady ochrany osobních údajů vaší organizace, tak interní zaměstnance a externí hosté můžou, projděte si zásady. Protože prohlášení o ochraně osobních údajů jsou jednoznačně vytvořen a přizpůsobené pro každou firmu, důrazně doporučujeme, abyste že požádali advokát žádostí o pomoc.

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-dsr-and-stp-note.md)]

## <a name="add-your-privacy-info-on-azure-ad"></a>Přidejte své informace o ochraně osobních údajů v Azure AD
Přidejte informace o ochraně osobních údajů vaší organizace v **vlastnosti** oblast služby Azure AD.

### <a name="to-access-the-properties-area-and-add-your-privacy-information"></a>Pro přístup k oblasti vlastnosti a přidejte své informace o ochranu osobních údajů

1.  Přihlaste se k webu Azure portal jako správce tenanta.

2.  V levém navigačním panelu vyberte **Azure Active Directory**a pak vyberte **vlastnosti**.

    **Vlastnosti** se zobrazí oblast.

    ![Zvýraznění oblasti informace o ochraně osobních údajů Azure AD vlastnosti oblasti](media/active-directory-properties-area/properties-area.png)

3.  Přidejte své informace o ochraně osobních údajů pro svoje zaměstnance:

    - **Technický kontakt.** Zadejte e-mailovou adresu osoby, obraťte se na technickou podporu v rámci vaší organizace.
    
    - **Obraťte se na globální ochrany osobních údajů.** Zadejte e-mailovou adresu pro osobu požádat o dotazy týkající se ochrany osobních údajů osobní údaje. Tato osoba je také kdo Microsoft obrátí, pokud dojde k porušení zabezpečení dat. Pokud neexistuje žádné osobě, tady najdete, kontaktuje Microsoft globální správci.

    - **Adresa URL prohlášení o ochraně osobních údajů.** Zadejte odkaz na dokument vaší organizace, popisující, jak organizace zpracovává i interních a externích hosta ochrany osobních údajů.

        >[!Important]
        >Pokud nechcete zahrnout vlastní prohlášení o ochraně osobních údajů nebo ochranu osobních údajů kontaktu, externí hosté se zobrazí text **kontrola oprávnění** pole,  **< _název vaší organizace_> neposkytl odkazy na podmínkách ke kontrole**. Při přijetí pozvánky pro přístup k organizaci prostřednictvím spolupráce B2B ve službě například uživatel typu Host se zobrazí tato zpráva.

        ![Oprávnění revize spolupráce B2B pole se zprávou](media/active-directory-properties-area/active-directory-no-privacy-statement-or-contact.png)

4.  Vyberte **Uložit**.

## <a name="next-steps"></a>Další postup
- [Uplatnění pozvání spolupráce služby Azure Active Directory s B2B](https://aka.ms/b2bredemption)
- [Přidání nebo změně informací profilu pro uživatele v Azure Active Directory](active-directory-users-profile-azure-portal.md)
