---
title: Postup odebrání přístupu uživatele k aplikaci | Dokumentace Microsoftu
description: Vysvětlení k odebrání přístupu uživatele k aplikaci
services: active-directory
documentationcenter: ''
author: barbkess
manager: daveba
ms.assetid: ''
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/17/2018
ms.author: barbkess
ms.openlocfilehash: 06c6f583cc5f4f0247dd47b5e2698086d7fe5b01
ms.sourcegitcommit: cf88cf2cbe94293b0542714a98833be001471c08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/23/2019
ms.locfileid: "54475692"
---
# <a name="how-to-remove-a-users-access-to-an-application"></a>Postup odebrání přístupu uživatele k aplikaci

Tento článek pomůže pochopit, jak odebrat přístup uživatelů k aplikaci.

## <a name="i-want-to-remove-a-specific-users-or-groups-assignment-to-an-application"></a>Chci odebrat konkrétní uživatele nebo skupiny přiřazení k aplikaci

Odebrání uživatele nebo skupiny přiřazení k aplikaci, postupujte podle kroků uvedených v [přiřazení uživatele nebo skupinu odebrat podnikové aplikace v Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-remove-assignment-azure-portal) článku.

## <a name="i-want-to-disable-all-access-to-an-application-for-every-user"></a>Chci zakázat veškerý přístup k aplikaci pro každého uživatele

K zakázání všech přihlášení uživatelů k aplikaci, postupujte podle kroků uvedených v [zakázat přihlášení uživatele pro podnikové aplikace v Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-disable-app-azure-portal) článku.

## <a name="i-want-to-delete-an-application-entirely"></a>Zcela odstranit aplikaci

K **odstranit aplikaci**, postupujte podle těchto pokynů:

1.  Otevřít [ **webu Azure portal** ](https://portal.azure.com/) a přihlaste se jako **globálního správce** nebo **Spolusprávce.**

2.  Otevřít **rozšíření Azure Active Directory** kliknutím **všechny služby** v horní části hlavní navigační nabídce vlevo.

3.  Zadejte **"Azure Active Directory**" do vyhledávacího pole filtrovat a vybrat **Azure Active Directory** položky.

4.  Klikněte na tlačítko **podnikové aplikace** levé navigační nabídce Azure Active Directory.

5.  Klikněte na tlačítko **všechny aplikace** zobrazíte seznam všech aplikací.

   * Pokud nevidíte aplikaci, kterou má zobrazit tady, použijte **filtr** ovládacího prvku v horní části **seznam všech aplikací** a nastavit **zobrazit** umožňuje **všechny Aplikace.**

6.  Vyberte aplikaci, kterou chcete odstranit.

7.  Po načtení aplikace, klikněte na tlačítko **odstranit** ikonu z hlavní aplikace **přehled** podokně.

## <a name="i-want-to-disable-all-future-user-consent-operations-to-any-application"></a>Chci zakázat všechny operace vyjádření souhlasu budoucí uživatele do žádné aplikace

Zakázání souhlas uživatele, pro celý adresář zabrání koncovým uživatelům od vyjádření souhlasu s libovolnou aplikaci. Správci můžou udělit souhlas stále na behalves uživatele. Další informace o aplikaci souhlas a proč může nebo nemusí chtít udělat, přečtěte si [uživatele porozumění a souhlas správce](../develop/howto-convert-app-to-be-multi-tenant.md#understand-user-and-admin-consent). Viz také [oprávnění a vyjádření souhlasu](../develop/v2-permissions-and-consent.md).

K **zakázat všechny operace vyjádření souhlasu budoucí uživatele v adresáři celý**, postupujte podle těchto pokynů:

1.  Otevřít [ **webu Azure portal** ](https://portal.azure.com/) a přihlaste se jako **globálního správce.**

2.  Otevřít **rozšíření Azure Active Directory** 

3.  Klikněte na tlačítko **podnikové aplikace** v navigační nabídce.

5.  Klikněte na tlačítko **uživatelská nastavení**.

6.  Nastavte **uživatelé můžou povolit aplikacím přístup k firemním datům jejich jménem** přepnutím **ne** a klikněte na tlačítko Uložit.


## <a name="next-steps"></a>Další postup

[Správa přístupu k aplikacím](what-is-access-management.md)
