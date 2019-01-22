---
title: Zapnutí pilotního nasazení samoobslužného resetování hesla služby Azure AD
description: V tomto kurzu zapnete samoobslužné resetování hesla služby Azure AD pro pilotní skupinu uživatelů.
services: active-directory
ms.service: active-directory
ms.component: authentication
ms.topic: tutorial
ms.date: 07/11/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahenry
ms.openlocfilehash: 21f2081f5aae0bb93cb9066407140f5fd35dc06d
ms.sourcegitcommit: 9999fe6e2400cf734f79e2edd6f96a8adf118d92
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/22/2019
ms.locfileid: "54424020"
---
# <a name="tutorial-complete-an-azure-ad-self-service-password-reset-pilot-roll-out"></a>Kurz: Kompletní Azure AD samoobslužné resetování hesla pilotní zavedení

V tomto kurzu zapnete ve své organizaci pilotní nasazení samoobslužného resetování hesla služby Azure AD a otestujete ho pomocí účtu bez oprávnění správce.

Testování samoobslužného resetování hesla musíte provést z účtů bez oprávnění správce. Zásady samoobslužného resetování hesla pro účty správců spravuje Microsoft a vyžaduje použití silnějších metod ověřování. Tyto zásady neumožňují použití bezpečnostních otázek a odpovědí a vyžadují použití dvou metod resetování.

> [!div class="checklist"]
> * Povolení samoobslužného resetování hesel
> * Test samoobslužného resetování hesel z uživatelského účtu

## <a name="prerequisites"></a>Požadavky

* Účet globálního správce

## <a name="enable-self-service-password-reset"></a>Povolení samoobslužného resetování hesel

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com) jako globální správce.
1. Přejděte do **Azure Active Directory** a vyberte **Resetování hesla**.
1. Začněte pilotní skupinou tak, že samoobslužné resetování hesla zapnete pouze určité skupině uživatelů v organizaci.
   * Na stránce **Vlastnosti** v části **Samoobslužné resetování hesla povoleno** zvolte **Vybraní** a vyberte pilotní skupinu.
      * Funkci samoobslužného resetování hesla mohou používat jenom členové vybrané skupiny Azure AD. Doporučujeme definovat skupinu uživatelů a toto nastavení použít při nasazení této funkce pro testování konceptu. Vnoření skupin zabezpečení se v tomto případě podporuje.
      * Přesvědčte se, že uživatelé ve vybrané skupině mají příslušnou licenci.
   * Klikněte na **Uložit**.
1. Na stránce **Metody ověřování**
   * Nastavte **Počet metod nutných pro resetování** na **2**.
   * Zvolte, které **Metody dostupné pro uživatele** chcete v organizaci povolit. Pro účely tohoto kurzu zaškrtněte políčka povolující **E-mail**, **Mobilní telefon** a **Telefon do kanceláře**.
   * Klikněte na **Uložit**.
1. Na stránce **Registrace**
   * U možnosti **Při přihlášení vyžadovat registraci uživatelů** vyberte **Ano**.
   * Nastavte **Počet dní před vyzváním uživatelů k potvrzení ověřovacích informací** na **180**.
   * Klikněte na **Uložit**.
1. Na stránce **Oznámení**
   * Možnost **Upozornit uživatele na resetování hesla** nastavte na **Ano**.
   * Možnost **Upozornit všechny správce na resetování hesla jiného správce** nastavte na **Ano**.
1. Na stránce **Přizpůsobení**
   * Microsoft doporučuje nastavit možnost **Přizpůsobit odkaz na helpdesk** na **Ano** a do pole **E-mail nebo adresa URL vlastního helpdesku** zadat e-mailovou adresu nebo adresu URL webové stránky, kde uživatelé získají další nápovědu od vaší organizace.
   * Pro účely tohoto kurzu nastavíme **Přizpůsobit odkaz na helpdesk** na **Ne**.

Samoobslužné resetování hesla je teď pro cloudové uživatele v pilotní skupině nakonfigurované.

## <a name="test-sspr-as-a-user"></a>Test samoobslužného resetování hesel z uživatelského účtu

Otestujte samoobslužné resetování hesla pomocí účtu uživatele bez oprávnění správce, který je členem pilotní skupiny. **Nezapomeňte, že pokud používáte účet s jakoukoli rolí správce, metody ověřování a jejich množství se může lišit, protože zásady pro správce spravuje Microsoft.**

1. Otevřete okno prohlížeče v režimu InPrivate nebo Incognito.
1. Pod účtem testovacího uživatele se zaregistrujte pomocí registračního portálu na adrese [https://aka.ms/ssprsetup](https://aka.ms/ssprsetup) k samoobslužnému resetování hesla.
1. Pod stejným účtem testovacího uživatele přejděte na portál samoobslužného resetování hesla na adrese [https://aka.ms/sspr](https://aka.ms/sspr) a pokuste se pomocí informací zadaných v předchozím kroku resetovat své heslo.
1. Resetování hesla by mělo proběhnout úspěšně.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud se rozhodnete, že už funkci nakonfigurovanou jako součást tohoto kurzu používat nechcete, proveďte následující.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
1. Přejděte do **Azure Active Directory** a vyberte **Resetování hesla**.
1. Na stránce **Vlastnosti** v části **Samoobslužné resetování hesla povoleno** zvolte **Žádné**.
1. Klikněte na **Uložit**.

## <a name="next-steps"></a>Další postup

V tomto kurzu jste zapnuli samoobslužné resetování hesla služby Azure AD. Pokračujte dalším kurzem, abyste zjistili, jak můžete integrovat místní infrastrukturu Active Directory Domain Services do prostředí samoobslužného resetování hesla.

> [!div class="nextstepaction"]
> [Povolení zpětného zápisu místní integrace SSPR](tutorial-enable-writeback.md)
