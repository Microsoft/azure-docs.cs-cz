---
title: Spravovat nastavení dvoustupňového ověřování – Azure AD | Dokumentace Microsoftu
description: Spravujte, jak pomocí Azure Multi-Factor Authentication, včetně změnu kontaktních informací nebo konfigurace zařízení.
services: multi-factor-authentication
keywords: vícefaktorové ověřování klienta, problém s ověřováním, ID korelace
documentationcenter: ''
author: eross-msft
manager: mtillman
ms.reviewer: richagi
ms.assetid: d3372d9a-9ad1-4609-bdcf-2c4ca9679a3b
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/23/2017
ms.author: lizross
ms.custom: end-user
ms.openlocfilehash: ff48cb2cee5387f5dd5e0b89966a11fb76379337
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/14/2018
ms.locfileid: "39059435"
---
# <a name="manage-your-settings-for-two-step-verification"></a>Správa nastavení pro dvoustupňové ověřování.
Tento článek obsahuje odpovědi na otázky o tom, jak aktualizovat nastavení pro dvoustupňové ověření nebo vícefaktorové ověřování. Pokud máte potíže s přihlášením k účtu, podívejte se na [potíže s dvoustupňovým ověřováním](multi-factor-authentication-end-user-troubleshoot.md) pomoc při řešení potíží.

## <a name="where-to-find-the-settings-page"></a>Kde najít na stránce nastavení
V závislosti na tom, jak vaše společnost nastavení ověřování Azure Multi-Factor Authentication jsou na několika místech, kde můžete změnit nastavení, jako je vaše telefonní číslo.

Pokud firemní podpora poslala na konkrétní adresu URL nebo kroky, chcete-li spravovat dvoustupňové ověřování, postupujte podle těchto pokynů. V opačném případě postupujte podle následujících pokynů by měla fungovat pro všem ostatním. Pokud postupujte podle těchto kroků, ale nevidíte stejné možnosti, znamená to, že vaše firma nebo škola přizpůsobit svoje vlastní portálu. Požádejte správce o odkaz na portál Azure Multi-Factor Authentication.

**Přejděte na stránku další bezpečnostní ověření**

- Přejděte do části https://aka.ms/MFASetup (Soubor > Nový > Jiné).

    ![Testovací](./media/multi-factor-authentication-end-user-manage-settings/proofup.png)

Pokud kliknete na tento odkaz není vhodná, můžete také získat k **dalšího ověření zabezpečení** stránku pomocí následujících kroků:

1. Přihlaste se k [https://myapps.microsoft.com](https://myapps.microsoft.com)  

2. Vyberte název svého účtu v pravém horním rohu a pak vyberte **profilu**.

3. Vyberte **dalšího ověření zabezpečení**.  

    ![Myapps](./media/multi-factor-authentication-end-user-manage-settings/myapps1.png)

4. Načte stránku další bezpečnostní ověření s nastavením.

    ![Testovací](./media/multi-factor-authentication-end-user-manage-settings/proofup.png)

## <a name="i-want-to-change-my-phone-number-or-add-a-secondary-number"></a>Chci změnit své telefonní číslo, nebo přidat sekundární číslo
Je potřeba nakonfigurovat sekundární ověřovací telefonní číslo.  Protože primárního telefonního čísla a mobilní aplikace jsou pravděpodobně na stejné telefonu, záložní telefonní číslo je jediný způsob, jak vám bude schopen se rychle vrátit ke svému účtu, pokud dojde ke ztrátě nebo odcizení telefonu.

> [!NOTE]
> Pokud nemáte přístup pro primární telefonní číslo a potřebujete pomoc získávání ke svému účtu, najdete v článku [potíže s dvoustupňovým ověřováním](multi-factor-authentication-end-user-troubleshoot.md) článek o další pomoc.  

**Chcete-li změnit primární telefonní číslo:**  

1. Na **dalšího ověření zabezpečení** stránky, vyberte textové pole s vaší aktuální telefonní číslo a upravovat pomocí nové telefonní číslo.  
2. Vyberte **Uložit**.  
3. Pokud toto telefonní číslo je číslo, které používáte pro vaši preferovanou možnost ověření, musíte ověřit nové číslo předtím, než jste jej uložili.  

**Chcete-li přidat záložní telefonní číslo:**  

1. Na stránce další bezpečnostní ověřování, zaškrtněte políčko vedle položky **telefon pro alternativní ověření.**  
2. Do textového pole zadejte záložní telefonní číslo.  
3. Vyberte **Uložit** a dokončí všechny potřebné změny.  

## <a name="require-two-step-verification-again-on-a-device-youve-marked-as-trusted"></a>Vyžadování dvoustupňového ověřování znovu na zařízení, které jste označili jako důvěryhodný

V závislosti na nastavení vaší organizace může mít zaškrtávací políčko s textem "nezobrazovat dotaz dalších **X** dní" při provádění dvoustupňové ověřování ve webovém prohlížeči. Pokud toto políčko zaškrtněte a potom ztráty zařízení nebo myslíte, že váš účet ohrožený, obnovíte dvoustupňové ověřování pro všechna vaše zařízení.

1. Na stránce další bezpečnostní ověření vyberte **obnovení služby Multi-Factor authentication u dřív důvěryhodných zařízení**.
2. Při příštím přihlášení na libovolném zařízení, budete vyzváni k provedení dvoustupňového ověření.

## <a name="how-do-i-clean-up-microsoft-authenticator-from-my-old-device-and-move-to-a-new-one"></a>Jak vyčistit prostředky z původní zařízení aplikaci Microsoft Authenticator a přesunout do nového?
Při odinstalaci aplikace ze zařízení nebo obnovení zařízení neodebere aktivace na back-endu. Další informace najdete v tématu [Microsoft Authenticator](microsoft-authenticator-app-how-to.md).

## <a name="next-steps"></a>Další postup
* Získejte tipy pro řešení potíží a Nápověda k [potíže s dvoustupňovým ověřováním](multi-factor-authentication-end-user-troubleshoot.md)
* Nastavit [hesla aplikací](multi-factor-authentication-end-user-app-passwords.md) pro všechny aplikace, ve kterých se dvoustupňové ověřování.
