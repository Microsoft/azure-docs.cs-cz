---
title: Přidávání a Správa aplikací Azure AD – Azure Marketplace
description: Naučte se přidávat a spravovat aplikace služby Azure AD pro program komerčního tržiště v partnerském centru.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
ms.date: 04/06/2021
author: varsha-sarah
ms.author: vavargh
ms.custom: contperf-fy21q2
ms.openlocfilehash: be527647466ad76455585e16baabb26e39e42193
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/08/2021
ms.locfileid: "107108238"
---
# <a name="add-and-manage-azure-ad-applications"></a>Přidávání a Správa aplikací Azure AD

**Příslušné role**

- Vlastník
- Manažer

Můžete dovolit aplikacím nebo službám, které jsou součástí Azure Active Directory vaší společnosti (Azure AD), získat přístup k vašemu účtu partnerského centra.

## <a name="add-existing-azure-ad-applications"></a>Přidat existující aplikace služby Azure AD

Chcete-li přidat aplikace, které již existují v Azure Active Directory vaší společnosti:

1. Na stránce **Uživatelé** (v části **Nastavení účtu**) vyberte **Přidat aplikace Azure AD**.
1. V seznamu, který se zobrazí, vyberte jednu nebo víc aplikací Azure AD. Pomocí vyhledávacího pole můžete vyhledat konkrétní aplikace služby Azure AD. Pokud vyberete více než jednu aplikaci Azure AD, kterou chcete přidat do svého účtu partnerského centra, musíte jim přiřadit stejnou roli nebo sadu vlastních oprávnění. Chcete-li přidat více aplikací služby Azure AD s různými rolemi a oprávněními, opakujte tento postup pro každou roli nebo sadu vlastních oprávnění.
1. Až budete hotovi s výběrem aplikací Azure AD, vyberte **Přidat vybrané**.
1. V části **role** určete role nebo vlastní oprávnění pro vybrané aplikace služby Azure AD.
1. Vyberte **Uložit**.

## <a name="add-new-azure-ad-applications"></a>Přidat nové aplikace Azure AD

Pokud chcete partnerskému centru udělit přístup k novému účtu aplikace služby Azure AD, můžete ho vytvořit v části **Uživatelé** . Tím se vytvoří nový účet v pracovním účtu vaší společnosti (tenant Azure AD), nikoli jenom v účtu partnerského centra. Pokud tuto aplikaci Azure AD používáte primárně pro ověřování partnerského centra a nepotřebujete, aby k nim uživatelé měli přístup přímo, můžete zadat libovolnou platnou adresu **adresy URL odpovědi** a **identifikátoru URI ID aplikace**, pokud tyto hodnoty nepoužívá žádná jiná aplikace služby Azure AD ve vašem adresáři.

1. Na stránce **Uživatelé** (v části **Nastavení účtu**) vyberte **Přidat aplikace Azure AD**.
1. Na další stránce vyberte **Nová aplikace Azure AD**.
1. Zadejte **adresu URL odpovědi** pro novou aplikaci Azure AD. Toto je adresa URL, kde se uživatelé můžou přihlásit a používat vaši aplikaci Azure AD (někdy taky označovanou jako adresa URL aplikace nebo adresa Sign-On URL). *Adresa URL odpovědi* nemůže být delší než 256 znaků a musí být v rámci vašeho adresáře jedinečná.
1. Zadejte **identifikátor URI ID aplikace** pro novou aplikaci Azure AD. Toto je logický identifikátor aplikace Azure AD, který se zobrazí při odeslání žádosti o jednotné přihlašování do Azure AD. *Identifikátor URI ID aplikace* musí být pro každou aplikaci Azure AD ve vašem adresáři jedinečný. Toto ID nemůže být delší než 256 znaků. Další informace o identifikátoru URI ID aplikace najdete v tématu [integrování aplikací pomocí Azure Active Directory](/azure/active-directory/develop/quickstart-modify-supported-accounts.md#change-the-application-registration-to-support-different-accounts).
1. V části **role** určete role nebo přizpůsobená oprávnění pro aplikaci Azure AD.
1. Vyberte **Uložit**.

Po přidání nebo vytvoření aplikace Azure AD se můžete vrátit do části **Uživatelé** a vybrat název aplikace pro kontrolu nastavení aplikace, včetně ID TENANTA, ID klienta, adresy URL odpovědi a identifikátoru URI ID aplikace.

## <a name="remove-an-azure-ad-application"></a>Odebrání aplikace Azure AD

Pokud chcete odebrat aplikaci z pracovního účtu (tenant Azure AD), pokračujte na **Uživatelé** (v části **Nastavení účtu**), vyberte aplikaci, kterou chcete odebrat, pomocí zaškrtávacího políčka ve sloupci úplně vpravo a pak z dostupných akcí zvolte **Odebrat** . Zobrazí se automaticky otevírané okno s potvrzením, že chcete vybrané aplikace odebrat.

## <a name="manage-keys-for-an-azure-ad-application"></a>Správa klíčů pro aplikaci Azure AD

Pokud vaše aplikace Azure AD čte a zapisuje data v Microsoft Azure AD, bude potřebovat klíč. Klíče pro aplikaci Azure AD můžete vytvořit úpravou informací v partnerském centru. Můžete také odebrat klíče, které už nepotřebujete.

1. Na stránce **Uživatelé** (v části **Nastavení účtu**) vyberte název aplikace Azure AD. Zobrazí se všechny aktivní klíče pro aplikaci Azure AD, včetně data, ve kterém byl klíč vytvořen, a jeho vypršení platnosti 50.
1. Pokud chcete odebrat klíč, který už nepotřebujete, vyberte **Odebrat**.
1. Chcete-li přidat nový klíč, vyberte možnost **Přidat nový klíč**.
1. Zobrazí se obrazovka s **ID klienta** a **klíčovými hodnotami**. Nezapomeňte tyto informace vytisknout nebo zkopírovat, protože po opuštění této stránky nebudete moct znovu získat přístup.
1. Pokud chcete vytvořit více klíčů, vyberte **Přidat další klíč**.
