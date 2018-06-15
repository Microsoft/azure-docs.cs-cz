---
title: Přidat informace o ochraně osobních údajů vaší organizace v Azure AD | Microsoft Docs
description: Vysvětluje, jak přidat do oblasti Azure Active Directory (Azure AD) vlastnosti informace o ochraně osobních údajů vaší organizace.
services: active-directory
documentationcenter: ''
author: eross-msft
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/17/2018
ms.author: lizross
ms.reviewer: bpham
ms.custom: it-pro
ms.openlocfilehash: 8cdf30ed09601a31529073eaedd4ab53780157d5
ms.sourcegitcommit: fc64acba9d9b9784e3662327414e5fe7bd3e972e
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/12/2018
ms.locfileid: "34077547"
---
# <a name="how-to-add-your-organizations-privacy-info-in-azure-active-directory"></a>Postupy: Přidání informace o ochraně osobních údajů vaší organizace v Azure Active Directory
Tento článek vysvětluje, jak správce klienta můžete přidat informace týkající se ochrany osobních údajů organizace klienta Azure Active Directory (Azure AD), prostřednictvím portálu Azure.

Důrazně doporučujeme přidat kontakt globální ochrany osobních údajů a prohlášení o ochraně osobních údajů vaší organizace, tak interní zaměstnance a externí hosté můžete si projděte zásady. Protože prohlášení o ochraně jednoznačně jsou vytvořeny a přizpůsobené pro každou obchodní, důrazně doporučujeme, že jste advokát požádejte o pomoc.

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-dsr-and-stp-note.md)]

## <a name="access-the-properties-area-to-add-your-privacy-info"></a>Přístup k oblasti vlastnosti přidat požadované informace o ochraně osobních údajů

1.  Přihlaste se k portálu Azure jako správce klienta.

2.  Na levé navigační panel, vyberte **Azure Active Directory**a potom vyberte **vlastnosti**.

    **Vlastnosti** se zobrazí oblast.

    ![Azure AD vlastnosti oblasti zvýraznění oblasti informace o ochraně osobních údajů](./media/active-directory-properties-area/properties-area.png)

3.  Přidejte požadované informace o ochraně osobních údajů pro vaši zaměstnanci:

    - **Obraťte se na technickou.** Zadejte e-mailovou adresu osoby, na obraťte se na technickou podporu v rámci vaší organizace.
    
    - **Obraťte se na globální ochrany osobních údajů.** Zadejte e-mailovou adresu pro osobu požádat o dotazy týkající se ochrany osobních údajů osobní data. Tato osoba je také kdo Microsoft kontaktuje, pokud je porušení data. Pokud není osoba, zde uvedeny, kontaktuje Microsoft globální správci.

    - **Adresa URL prohlášení o ochraně osobních údajů.** Zadejte odkaz na dokument vaší organizace, který popisuje, jak vaše organizace zpracuje obě interních a externích hosta ochrany osobních údajů.

        >[!Important]
        >Pokud nezadáte vlastní prohlášení o ochraně osobních údajů nebo kontaktu ochrany osobních údajů, externí hosté zobrazí text v **zkontrolujte oprávnění** pole s informacemi o tom,  **< _název vaší organizace_> nebyl poskytuje odkazy na jejich podmínky pro vás k posouzení**. Například uživatel typu Host se zobrazí tato zpráva, když dostanou pozvánka k organizaci prostřednictvím spolupráce B2B.

        ![Políčka oprávnění Zkontrolujte spolupráce B2B se zprávou](./media/active-directory-properties-area/active-directory-no-privacy-statement-or-contact.png)

4.  Vyberte **Uložit**.

## <a name="next-steps"></a>Další postup
- [Azure Active Directory s B2B spolupráce pozvánku k uplatnění kódu.](https://aka.ms/b2bredemption)
- [Přidat nebo změnit informace o profilu pro uživatele v Azure Active Directory](/active-directory-users-profile-azure-portal.md)