---
title: Správa zařízení pomocí webu Azure portal | Dokumentace Microsoftu
description: Zjistěte, jak pomocí webu Azure portal ke správě zařízení.
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
ms.date: 08/25/2018
ms.author: markvi
ms.reviewer: jairoc
ms.openlocfilehash: 7aa7cd2e5b1cf1346a5a7b1f82ec9c7933dd1112
ms.sourcegitcommit: f983187566d165bc8540fdec5650edcc51a6350a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/13/2018
ms.locfileid: "45542476"
---
# <a name="how-to-manage-devices-using-the-azure-portal"></a>Správa zařízení pomocí webu Azure portal


Díky správě zařízení v Azure Active Directory (Azure AD) můžete zajistit, aby uživatelé přistupovali k vašim prostředkům ze zařízení, která splňují vaše standardy zabezpečení a dodržování předpisů. 

Tento článek:

- Předpokládá, že máte zkušenosti s [Úvod ke správě zařízení ve službě Azure Active Directory](overview.md)

- Poskytuje informace o správě vašeho zařízení pomocí webu Azure portal

## <a name="manage-devices"></a>Správa zařízení 

Na webu Azure portal vám poskytne centrální místo, kde můžete spravovat vaše zařízení. Můžete získat na tomto místě buď pomocí [přímý odkaz](https://portal.azure.com/#blade/Microsoft_AAD_IAM/DevicesMenuBlade/Devices) nebo ruční takto:

1. Přihlaste se k [webu Azure portal](https://portal.azure.com) jako správce.

2. Na levém navigačním panelu klikněte na tlačítko **služby Active Directory**.

    ![Konfigurovat nastavení zařízení](./media/device-management-azure-portal/01.png)

3. V **spravovat** klikněte na tlačítko **zařízení**.

    ![Konfigurovat nastavení zařízení](./media/device-management-azure-portal/74.png)
 
**Zařízení** stránka umožňuje:

- Konfigurovat nastavení správy zařízení

- Najít zařízení

- Provádění úloh správy zařízení

- Zkontrolujte zařízení správy související s protokoly auditu  
  

## <a name="configure-device-settings"></a>Konfigurovat nastavení zařízení

Ke správě zařízení pomocí webu Azure portal, musí být buď vaše zařízení [zaregistrovaných nebo spojených](overview.md#getting-devices-under-the-control-of-azure-ad) do služby Azure AD. Jako správce můžete optimalizovat proces registrace a připojení zařízení tím, že nakonfigurujete nastavení zařízení. 

![Konfigurovat nastavení zařízení](./media/device-management-azure-portal/22.png)

Na stránce nastavení zařízení můžete nakonfigurovat:

![Správa zařízení s Intune](./media/device-management-azure-portal/21.png)


- **Uživatelé můžou připojovat zařízení do služby Azure AD** – toto nastavení umožňuje vybrat uživatele, kteří můžou [připojovat zařízení](overview.md#azure-ad-joined-devices) do služby Azure AD. Výchozí hodnota je **všechny**. Toto nastavení platí jenom pro Azure AD Join ve Windows 10.

- **Zařízení připojená k další místní správci na Azure AD** – můžete vybrat uživatele, kteří jsou udělena práva místního správce v zařízení. Uživatelé přidaní Zde jsou přidány do *Správci zařízení* role ve službě Azure AD. Globální správci ve službě Azure AD a vlastníci zařízení jsou ve výchozím nastavení udělena práva místního správce. Tato možnost je funkce edice premium, která je dostupná prostřednictvím produktů, jako je Azure AD Premium nebo Enterprise Mobility Suite (EMS). 

- **Uživatelé můžou registrovat svoje zařízení s Azure AD** – budete potřebovat ke konfiguraci tohoto nastavení můžete zařízením povolit, aby se [zaregistrovaný](overview.md#azure-ad-registered-devices) s Azure AD. Pokud vyberete **žádný**, zařízení nemůžou zaregistrovat, pokud nejsou připojená k Azure AD nebo Azure AD připojená k hybridní službě. Registrace pomocí Microsoft Intune nebo Správa mobilních zařízení (MDM) pro Office 365 vyžaduje registrace. Pokud jste nakonfigurovali některou z těchto služeb **všechny** je vybraná a **NONE** není k dispozici.

- **Vyžadovat Vícefaktorové ověřování pro připojení zařízení** – můžete zvolit, jestli musí uživatelé provést poskytnout druhý ověřovací faktor k [spojení](overview.md#azure-ad-joined-devices) své zařízení do služby Azure AD. Výchozí hodnota je **ne**. Doporučujeme, abyste vyžadování vícefaktorového ověřování při registraci zařízení. Před povolením služby Multi-Factor authentication pro tuto službu, musíte zajistit, že je, že ověřování službou Multi-Factor Authentication nakonfigurováno pro uživatele, které registrují svá zařízení. Další informace o službách různé služby Azure Multi-Factor authentication, naleznete v tématu [Začínáme se službou Azure Multi-Factor authentication](../authentication/concept-mfa-whichversion.md). Toto nastavení nemá vliv připojení k hybridní službě pro Windows 10 nebo Windows 7. To platí pouze pro připojení ke službě Azure AD na Windows 10 a vlastní registraci zařízení pro Windows 10, iOS a Android. 

- **Maximální počet zařízení** – toto nastavení umožňuje vybrat maximální počet zařízení, která uživatel může mít ve službě Azure AD. Pokud uživatel dosáhne této kvóty, jejich nejsou možné přidat další zařízení do jednoho nebo více stávající zařízení se odeberou. Nabídka zařízení se bude počítat pro všechna zařízení, která jsou připojená k Azure AD nebo Azure AD registrované ještě dnes. Výchozí hodnota je **20**.

- **Uživatelé můžou synchronizovat nastavení a data aplikací na zařízeních** – ve výchozím nastavení, toto nastavení nastavené na **NONE**. Vyberte konkrétní uživatele nebo skupiny nebo všechny umožňuje uživatelského nastavení a data aplikací synchronizovat na zařízení s Windows 10. Další informace o tom, jak funguje synchronizace ve Windows 10.
Tato možnost je dostupná prostřednictvím produktů, jako je Azure AD Premium nebo Enterprise Mobility Suite (EMS) funkce úrovně premium.
 




## <a name="locate-devices"></a>Najít zařízení

Máte dvě možnosti k vyhledání zařízení zaregistrovaná a připojené k doméně:

- **Všechna zařízení** v **spravovat** část **zařízení** stránky  

    ![Všechna zařízení](./media/device-management-azure-portal/41.png)


- **Zařízení** v **spravovat** část **uživatele** stránky
 
    ![Všechna zařízení](./media/device-management-azure-portal/43.png)



U obou možností můžete získat zobrazení, které:


- Umožňuje vyhledat zařízení pomocí názvu zobrazení jako filtr.

- Poskytuje podrobný přehled o zařízeních registrovaných a připojené k doméně

- Umožňuje provádět běžné úlohy správy zařízení
   

![Všechna zařízení](./media/device-management-azure-portal/51.png)

Pro některá zařízení s Iosem můžete použít názvy zařízení obsahující apostrofy potenciálně různých znaků, které vypadají jako apostrofy. Vyhledávání těchto zařízení je trochu složité – Pokud se nezobrazují výsledky hledání správně, ujistěte se prosím, že hledaný řetězec obsahuje odpovídající znak apostrofu.

## <a name="device-management-tasks"></a>Úlohy správy zařízení

Jako správce můžete spravovat zařízení registrovaná nebo připojené k doméně. Tato část obsahuje informace o běžných úloh správy zařízení.


### <a name="manage-an-intune-device"></a>Správa zařízení s Intune

Pokud jste správce služby Intune, můžete spravovat zařízení, které jsou označeny jako **Microsoft Intune**. 

![Správa zařízení s Intune](./media/device-management-azure-portal/31.png)


### <a name="enable--disable-an-azure-ad-device"></a>Povolit / zakázat zařízení s Azure AD

Chcete-li povolit / zakázat zařízení, máte dvě možnosti:

- Nabídka úkoly ("...") na **všechna zařízení** stránky

    ![Správa zařízení s Intune](./media/device-management-azure-portal/71.png)

- Na panelu nástrojů **zařízení** stránky

    ![Správa zařízení s Intune](./media/device-management-azure-portal/32.png)


**Poznámky:**

- Musíte být globálním správcem ve službě Azure AD pro povolení / zákaz zařízení. 
- Zařízení zakázáním zařízení brání v přístupu k prostředkům Azure AD. 



### <a name="delete-an-azure-ad-device"></a>Odstranit zařízení s Azure AD

Pokud chcete odstranit zařízení, máte dvě možnosti:

- Nabídka úkoly ("...") na **všechna zařízení** stránky

    ![Správa zařízení s Intune](./media/device-management-azure-portal/72.png)

- Na panelu nástrojů **zařízení** stránky

    ![Odstranění zařízení](./media/device-management-azure-portal/34.png)


**Poznámky:**

- Musíte být globálním správcem nebo správcem Intune v Azure AD, pokud chcete odstranit zařízení.

- Odstraňuje se zařízení:
 
    - Zařízení brání v přístupu k prostředkům Azure AD. 

    - Odebere všechny podrobnosti, které jsou připojeny k zařízení, například, klíče Bitlockeru pro zařízení s Windows.  

    - Představuje neobnovitelná aktivitu a se nedoporučuje, pokud je povinný.

Pokud je zařízení spravováno nástrojem jinou autoritu pro správu (například Microsoft Intune), ujistěte se prosím, že má zařízení dojde k vymazání / vyřazení před odstraněním tohoto zařízení ve službě Azure AD.

 


### <a name="view-or-copy-device-id"></a>Zobrazení nebo zkopírovat ID zařízení

ID zařízení můžete ověřit ID podrobností o zařízení na zařízení nebo při řešení potíží s použitím prostředí PowerShell. Pro přístup k možnosti kopírování, klikněte na zařízení.

![Zobrazit ID zařízení](./media/device-management-azure-portal/35.png)
  

### <a name="view-or-copy-bitlocker-keys"></a>Zobrazení nebo kopie klíče Bitlockeru

Můžete zobrazení a zkopírování klíče Bitlockeru k poskytování pomoci uživatelům obnovit svá šifrované jednotce. Tyto klíče jsou dostupné jenom pro zařízení Windows, které jsou šifrované a jejich klíče uloženého ve službě Azure AD. Při přístupu k podrobnosti o zařízení, můžete zkopírovat tyto klíče.
 
![Zobrazovat klíče Bitlockeru](./media/device-management-azure-portal/36.png)

k zobrazení nebo zkopírování klíče nástroje BitLocker, musíte být vlastníkem zařízení nebo uživatel, který má alespoň jednu z následujících rolí přiřadit:

- Globální správci
- Správci technické podpory
- Správce zabezpečení
- Čtenáři zabezpečení
- Správci služby Intune

> [!NOTE]
> Zařízení s Windows 10 připojená k hybridní službě Azure AD nemá vlastníka. Ano Pokud hledáte zařízení podle vlastníka a nebyl nalezen, vyhledávání podle ID zařízení.


## <a name="audit-logs"></a>Protokoly auditu


Aktivity zařízení jsou k dispozici prostřednictvím protokolů aktivit. To zahrnuje aktivity aktivuje službu device registration service a uživatelé:

- Vytvoření zařízení a přidávají se vlastníci / uživatele v zařízení.

- Změny v nastavení zařízení

- Operace zařízení, jako je například odstranění nebo aktualizaci zařízení
 
Vaším vstupním bodem k datům auditování je **protokoly auditu** v **aktivity** část **zařízení** stránky.

![Protokoly auditu](./media/device-management-azure-portal/61.png)


Protokol auditu má výchozí zobrazení seznamu, které obsahuje následující položky:

- Datum a čas výskytu

- Cíle

- Iniciátor / objektu actor (kdo) aktivity

- Aktivita (co)

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

Kromě filtrů můžete vyhledat specifické položky.

![Protokoly auditu](./media/device-management-azure-portal/65.png)

## <a name="next-steps"></a>Další postup

* [Úvod do správy zařízení v Azure Active Directory](overview.md)



