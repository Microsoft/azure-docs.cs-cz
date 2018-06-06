---
title: Správa zařízení pomocí portálu Azure | Microsoft Docs
description: Další informace o použití portálu Azure ke správě zařízení.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: mtillman
editor: ''
ms.assetid: 54e1b01b-03ee-4c46-bcf0-e01affc0419d
ms.service: active-directory
ms.component: devices
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/01/2018
ms.author: markvi
ms.reviewer: jairoc
ms.openlocfilehash: ddce312d6299a7749f1a40a83f67ce3c26876591
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/04/2018
ms.locfileid: "34713996"
---
# <a name="managing-devices-using-the-azure-portal"></a>Správa zařízení pomocí portálu Azure


Se správou zařízení ve službě Azure Active Directory (Azure AD) můžete zajistit, že vaši uživatelé přistupují k prostředkům ze zařízení, která splňují vaše standardy zabezpečení a dodržování předpisů. 

Tento článek:

- Předpokládá, že jste obeznámeni s [Úvod do správy zařízení v Azure Active Directory](device-management-introduction.md)

- Poskytuje informace o správě zařízení pomocí portálu Azure

## <a name="manage-devices"></a>Správa zařízení 

Portál Azure poskytuje centrální místo ke správě svých zařízení. Můžete získat na toto místo buď pomocí [přímý odkaz](https://portal.azure.com/#blade/Microsoft_AAD_IAM/DevicesMenuBlade/Devices) nebo pomocí následujících kroků ruční:

1. Přihlaste se k [portál Azure](https://portal.azure.com) jako správce.

2. V levém navigační panel, klikněte na tlačítko **služby Active Directory**.

    ![Konfigurovat nastavení zařízení](./media/device-management-azure-portal/01.png)

3. V **spravovat** klikněte na tlačítko **zařízení**.

    ![Konfigurovat nastavení zařízení](./media/device-management-azure-portal/11.png)
 
**Zařízení** stránka vám umožňuje:

- Konfigurovat nastavení správy zařízení

- Vyhledávání zařízení

- Provádění úloh správy zařízení

- Zkontrolujte správy zařízení, související protokoly auditu  
  

## <a name="configure-device-settings"></a>Konfigurovat nastavení zařízení

Ke správě zařízení pomocí portálu Azure, zařízení musí být buď [zaregistrovaný nebo připojený k](device-management-introduction.md#getting-devices-under-the-control-of-azure-ad) do služby Azure AD. Jako správce můžete optimalizovat proces registrace a připojení zařízení tak, že nakonfigurujete nastavení zařízení. 

![Konfigurovat nastavení zařízení](./media/device-management-azure-portal/22.png)

Stránka nastavení zařízení můžete konfigurovat:

![Spravovat zařízení s Intune](./media/device-management-azure-portal/21.png)


- **Uživatelé mohou připojit zařízení ke službě Azure AD** – toto nastavení umožňuje vybrat uživatele, kteří mohou [připojit zařízení](device-management-introduction.md#azure-ad-joined-devices) do služby Azure AD. Výchozí hodnota je **všechny**.

- **Zařízení připojená k další místní správci v Azure AD** – můžete vybrat uživatele, kteří jsou udělena práva místního správce v zařízení. Jsou přidáni uživatelé tady přidat *Správci zařízení* role ve službě Azure AD. Globální správci ve službě Azure AD a vlastníci zařízení jsou udělena práva místního správce ve výchozím nastavení. Tato možnost je k dispozici prostřednictvím produkty, jako je například Azure AD Premium nebo Enterprise Mobility Suite (EMS) funkce edice premium. 

- **Uživatelé mohou registrovat svá zařízení s Azure AD** -je nutné nakonfigurovat toto nastavení umožňuje zařízení, aby vyhovoval [zaregistrován](device-management-introduction.md#azure-ad-registered-devices) s Azure AD. Pokud vyberete **žádné**, zařízení není povoleno zaregistrovat, které nejsou připojené k Azure AD nebo hybridní připojený k Azure AD. Registrace s Microsoft Intune nebo správy mobilních zařízení (MDM) pro Office 365 vyžaduje registraci. Pokud jste nakonfigurovali některou z těchto služeb **všechny** je vybraná a **NONE** není k dispozici...

- **Vyžadovat vícefaktorového ověřování pro připojení zařízení** – můžete zvolit, jestli uživatelé musí poskytnout druhý ověřovací faktor k [spojení](device-management-introduction.md#azure-ad-joined-devices) své zařízení do služby Azure AD. Výchozí hodnota je **ne**. Doporučujeme, abyste při registraci zařízení, které vyžadují služby Multi-Factor authentication. Než povolíte službu Multi-Factor authentication pro tuto službu, je nutné zajistit, že aplikace Multi-Factor authentication je nakonfigurován pro uživatele, kteří registrovat svá zařízení. Další informace o službám různých Azure Multi-Factor authentication, naleznete v části [Začínáme s Azure Multi-Factor authentication](authentication/concept-mfa-whichversion.md). 

- **Maximální počet zařízení** – toto nastavení umožňuje vybrat maximální počet zařízení, které uživatel může mít ve službě Azure AD. Pokud uživatel dosáhne této kvóty, budou se moct přidávat další zařízení, dokud jeden nebo více existující zařízení se odeberou. Uvozovky zařízení se počítá pro všechna zařízení, které jsou připojené k Azure AD nebo dnes zaregistrovat Azure AD. Výchozí hodnota je **20**.

- **Uživatelé můžou synchronizovat nastavení a dat aplikací mezi zařízeními** – ve výchozím nastavení, toto nastavení je **NONE**. Výběr konkrétní uživatele nebo skupiny nebo všechny umožňuje uživatelské nastavení a dat aplikací na synchronizaci přes své zařízení s Windows 10. Další informace o tom, jak funguje synchronizace ve Windows 10.
Tato možnost je k dispozici prostřednictvím produkty, jako je například Azure AD Premium nebo Enterprise Mobility Suite (EMS) premium funkce.
 




## <a name="locate-devices"></a>Vyhledávání zařízení

Máte dvě možnosti vyhledávání zaregistrovaná a připojené k zařízení:

- **Všechna zařízení** v **spravovat** části **zařízení** stránky  

    ![Všechna zařízení](./media/device-management-azure-portal/41.png)


- **Zařízení** v **spravovat** části **uživatele** stránky
 
    ![Všechna zařízení](./media/device-management-azure-portal/43.png)



Obě možnosti můžete získat zobrazení který:


- Umožňuje vyhledat zařízení pomocí zobrazovaný název jako filtr.

- Poskytuje podrobný přehled o zaregistrovaná a připojené k zařízení

- Umožňuje provádět běžné úlohy správy zařízení
   

![Všechna zařízení](./media/device-management-azure-portal/51.png)

Pro některá zařízení s iOS můžete použít názvy zařízení obsahující uvozovky potenciálně různých znaků, které vypadají podobně jako apostrofy. Hledání takovým zařízením je trochu složité – Pokud nevidíte výsledky hledání správně, zkontrolujte, že hledaný řetězec obsahuje apostrof odpovídající.

## <a name="device-management-tasks"></a>Úlohy správy zařízení

Jako správce můžete spravovat registrovaná nebo připojeného k zařízení. Tato část poskytuje informace o běžných úlohách správy zařízení.


### <a name="manage-an-intune-device"></a>Spravovat zařízení s Intune

Pokud jste správce služby Intune, můžete spravovat zařízení, které jsou označeny jako **Microsoft Intune**. Správce můžete zobrazit další zařízení 

![Spravovat zařízení s Intune](./media/device-management-azure-portal/31.png)


### <a name="enable--disable-an-azure-ad-device"></a>Povolit / zakázat zařízení s Azure AD

Chcete-li povolit / zakázat zařízení, máte dvě možnosti:

- Nabídka úkoly ("...") na **všechna zařízení** stránky

    ![Spravovat zařízení s Intune](./media/device-management-azure-portal/71.png)

- Na panelu nástrojů **zařízení** stránky

    ![Spravovat zařízení s Intune](./media/device-management-azure-portal/32.png)


**Poznámky:**

- Musíte být globálním správcem ve službě Azure AD se povolit nebo zakázat zařízení. 
- Zakázáním zařízení tomuto zařízení brání přístupu k prostředkům Azure AD. 



### <a name="delete-an-azure-ad-device"></a>Odstranění zařízení s Azure AD

Pokud chcete odstranit zařízení, máte dvě možnosti:

- Nabídka úkoly ("...") na **všechna zařízení** stránky

    ![Spravovat zařízení s Intune](./media/device-management-azure-portal/72.png)

- Na panelu nástrojů **zařízení** stránky

    ![Odstranění zařízení](./media/device-management-azure-portal/34.png)


**Poznámky:**

- Musíte být globálním správcem ve službě Azure AD se odstranit zařízení.  

- Odstranění zařízení:
 
    - Tomuto zařízení brání přístupu k prostředkům Azure AD. 

    - Odebere všechny podrobnosti, které jsou připojené k zařízení, například, klíče Bitlockeru pro zařízení se systémem Windows.  

    - Představuje neobnovitelná aktivity a se nedoporučuje, pokud je potřeba.

Pokud se zařízení spravuje jiná autoritu pro správu (například Microsoft Intune), ujistěte se, že má zařízení vymazání / vyřazení před odstraněním zařízení ve službě Azure AD.

 


### <a name="view-or-copy-device-id"></a>Zobrazení nebo zkopírujte ID zařízení

ID zařízení můžete použít k ověření informace ID zařízení na zařízení nebo pomocí prostředí PowerShell při řešení potíží. Pro přístup k možnosti kopírování, klikněte na zařízení.

![Zobrazit ID zařízení](./media/device-management-azure-portal/35.png)
  

### <a name="view-or-copy-bitlocker-keys"></a>Zobrazení nebo kopírování klíče nástroje BitLocker

Můžete zobrazit a zkopírujte tyto klíče nástroje BitLocker pomoct uživatelům obnovit svá šifrované jednotce. Tyto klíče jsou dostupné pouze pro zařízení s Windows, které jsou zašifrované a jejich klíče uloženého ve službě Azure AD. Při přístupu k podrobnosti o zařízení, můžete zkopírovat tyto klíče.
 
![Klíče Bitlockeru zobrazení](./media/device-management-azure-portal/36.png)

k zobrazení nebo zkopírujte tyto klíče nástroje BitLocker, musíte být vlastníkem zařízení nebo uživatele, který má alespoň jeden z následujících role přiřazené:

- Globální správci
- Správci technické podpory
- Správce zabezpečení
- Čtečky zabezpečení
- Správci služby Intune


## <a name="audit-logs"></a>Protokoly auditu


Aktivity zařízení jsou dostupné přes protokoly aktivity. To zahrnuje aktivity spustí službu device registration service a uživatelé:

- Vytváření zařízení a přidání vlastníky nebo uživatele v zařízení.

- Změny nastavení zařízení

- Operace zařízení například odstranění nebo aktualizaci zařízení
 
Vašim vstupním bodem k datům auditování je **protokoly auditu** v **aktivity** části **zařízení** stránky.

![Protokoly auditu](./media/device-management-azure-portal/61.png)


Protokol auditu má výchozí zobrazení seznamu, které obsahuje následující položky:

- Datum a čas výskytu

- cíle

- iniciátoru nebo objektu actor (který) aktivity

- aktivity (co)

![Protokoly auditu](./media/device-management-azure-portal/63.png)

Zobrazení seznamu můžete upravit kliknutím na **Sloupce** na panelu nástrojů.
 
![Protokoly auditu](./media/device-management-azure-portal/64.png)


Abyste omezili zobrazovaná data na úroveň, která vám vyhovuje, můžete filtrovat data přihlašování s využitím následujících polí:

- Kategorie
- Typ prostředku aktivity
- Aktivita
- Rozsah dat
- Cíl
- Iniciátor (actor)

Kromě filtry můžete vyhledat konkrétní položky.

![Protokoly auditu](./media/device-management-azure-portal/65.png)

## <a name="next-steps"></a>Další postup

* [Úvod do správy zařízení v Azure Active Directory](device-management-introduction.md)



