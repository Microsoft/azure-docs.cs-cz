---
title: Přidání informací o ochraně osobních údajů vaší organizace – Azure Active Directory | Dokumenty společnosti Microsoft
description: Pokyny, jak přidat informace o ochraně osobních údajů vaší organizace do oblasti Vlastnosti služby Azure Active Directory.
services: active-directory
author: msaburnley
manager: daveba
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 04/17/2018
ms.author: ajburnle
ms.reviewer: bpham
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 343671c1d9ee82950a9822648f9831588da7e9f8
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/08/2020
ms.locfileid: "80876180"
---
# <a name="add-your-organizations-privacy-info-using-azure-active-directory"></a>Přidání informací o ochraně osobních údajů vaší organizace pomocí Služby Azure Active Directory
Tento článek vysvětluje, jak může správce tenanta přidat informace o ochraně osobních údajů do tenanta Azure Directory (Azure AD) organizace prostřednictvím portálu Azure.

Důrazně doporučujeme přidat váš globální kontakt na ochranu osobních údajů a prohlášení o ochraně osobních údajů vaší organizace, aby vaši interní zaměstnanci a externí hosté mohli zkontrolovat vaše zásady. Vzhledem k tomu, že prohlášení o ochraně osobních údajů jsou jedinečně vytvořena a přizpůsobena pro každou firmu, důrazně doporučujeme, abyste se obrátili na právníka s žádostí o pomoc.

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-dsr-and-stp-note.md)]

## <a name="add-your-privacy-info-on-azure-ad"></a>Přidání informací o ochraně osobních údajů ve službě Azure AD
Informace o ochraně osobních údajů vaší organizace přidáte do oblasti **Vlastnosti** ve službě Azure AD.

### <a name="to-access-the-properties-area-and-add-your-privacy-information"></a>Přístup k oblasti Vlastnosti a přidání informací o ochraně osobních údajů

1.    Přihlaste se k portálu Azure jako správce klienta.

2.    Na levém panelu vyberte **Azure Active Directory**a pak vyberte **Vlastnosti**.

    Zobrazí se oblast **Vlastnosti.**

    ![Oblast Azure AD Properties zvýrazňující oblast informací o ochraně osobních údajů](media/active-directory-properties-area/properties-area.png)

3.    Přidejte své informace o ochraně osobních údajů pro své zaměstnance:

    - **Technický kontakt.** Zadejte e-mailovou adresu osoby, kterou má osoba kontaktovat s žádostí o technickou podporu ve vaší organizaci.
    
    - **Globální kontakt na ochranu osobních údajů.** Zadejte e-mailovou adresu, na kterou se má daná osoba obrátit s dotazy týkajícími se ochrany osobních údajů. Tato osoba je také tím, s kým společnost Microsoft kontaktuje v případě porušení zabezpečení dat. Pokud zde není uvedena žádná osoba, společnost Microsoft kontaktuje globální správce.

    - **Adresa URL prohlášení o zásadách ochrany osobních údajů.** Zadejte odkaz na dokument vaší organizace, který popisuje, jak vaše organizace zpracovává ochranu osobních údajů interních i externích hostů.

        >[!Important]
        >Pokud nezahrnete vlastní prohlášení o zásadách ochrany osobních údajů ani kontakt na ochranu osobních údajů, zobrazí se vašim externím hostům text v poli **Oprávnění k recenzování,** ve kterém je uvedeno, ** <že _název vaší organizace_> neposkytl odkazy na jejich podmínky, které byste měli zkontrolovat**. Například uživatel typu Host se zobrazí tato zpráva, když obdrží pozvánku pro přístup k organizaci prostřednictvím spolupráce B2B.

        ![B2B Spolupráce Oprávnění revize se zprávou](media/active-directory-properties-area/active-directory-no-privacy-statement-or-contact.png)

4.    Vyberte **Uložit**.

## <a name="next-steps"></a>Další kroky
- [Využití pozvánky ke spolupráci služby Azure Active Directory B2B](../b2b/redemption-experience.md)
- [Přidání nebo změna informací o profilu uživatele ve službě Azure Active Directory](active-directory-users-profile-azure-portal.md)
