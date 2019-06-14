---
title: Správa zařízení pomocí webu Azure portal | Dokumentace Microsoftu
description: Zjistěte, jak pomocí webu Azure portal ke správě zařízení.
services: active-directory
documentationcenter: ''
author: MicrosoftGuyJFlo
manager: daveba
editor: ''
ms.assetid: 54e1b01b-03ee-4c46-bcf0-e01affc0419d
ms.service: active-directory
ms.subservice: devices
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/04/2019
ms.author: joflore
ms.reviewer: jairoc
ms.collection: M365-identity-device-management
ms.openlocfilehash: 48f162e5e6eb29e4a658000826ccf25389086342
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "66730438"
---
# <a name="manage-device-identity-using-the-azure-portal"></a>Správa identit zařízení pomocí webu Azure portal

Správa identit zařízení ve službě Azure Active Directory (Azure AD) můžete zajistit, že vaši uživatelé přistupují ze zařízení, která splňují vaše standardy zabezpečení a dodržování předpisů vašich prostředků.

Tento článek:

- Předpokládá, že máte zkušenosti s [seznámení se správou identit zařízení ve službě Azure Active Directory](overview.md)
- Poskytuje informace o správě identit zařízení pomocí portálu Azure AD

## <a name="manage-device-identities"></a>Správa identit zařízení

Na portálu Azure AD vám poskytne centrální místo pro správu identit zařízení. Můžete získat na tomto místě buď pomocí [přímý odkaz](https://portal.azure.com/#blade/Microsoft_AAD_IAM/DevicesMenuBlade/Devices) nebo ruční takto:

1. Přihlaste se k [webu Azure portal](https://portal.azure.com) jako správce.
2. Na levém navigačním panelu klikněte na tlačítko **služby Active Directory**.

   ![Konfigurace nastavení zařízení](./media/device-management-azure-portal/01.png)

3. V **spravovat** klikněte na tlačítko **zařízení**.

   ![Konfigurace nastavení zařízení](./media/device-management-azure-portal/74.png)

**Zařízení** stránka umožňuje:

- Konfigurace nastavení zařízení
- Najít zařízení
- Provádění úloh správy identity zařízení
- Kontrolovat protokoly auditu zařízení:  
  
## <a name="configure-device-settings"></a>Konfigurace nastavení zařízení

Správa identit zařízení pomocí portálu Azure AD, musí být buď vaše zařízení [zaregistrovaných nebo spojených](overview.md) do služby Azure AD. Jako správce můžete optimalizovat proces registrace a připojení zařízení tím, že nakonfigurujete nastavení zařízení.

![Konfigurace nastavení zařízení](./media/device-management-azure-portal/22.png)

Na stránce nastavení zařízení můžete nakonfigurovat:

![Správa zařízení s Intune](./media/device-management-azure-portal/21.png)

- **Uživatelé můžou připojovat zařízení do služby Azure AD** – toto nastavení umožňuje vybrat uživatele, kteří mohou registrovat svá zařízení jako [zařízení Azure AD join](overview.md#azure-ad-joined-devices). Výchozí hodnota je **všechny**.

>[!NOTE]
> **Uživatelé můžou připojovat zařízení do služby Azure AD** nastavení platí jenom pro připojení ke službě Azure AD na Windows 10.

- **Zařízení připojená k další místní správci na Azure AD** – můžete vybrat uživatele, kteří jsou udělena práva místního správce v zařízení. Uživatelé přidaní Zde jsou přidány do *Správci zařízení* role ve službě Azure AD. Globální správci ve službě Azure AD a vlastníci zařízení jsou ve výchozím nastavení udělena práva místního správce. Tato možnost je funkce edice premium, která je dostupná prostřednictvím produktů, jako je Azure AD Premium nebo Enterprise Mobility Suite (EMS).
- **Uživatelé můžou registrovat svoje zařízení s Azure AD** – budete potřebovat ke konfiguraci tohoto nastavení můžete povolit osobní, iOS, Android a macOs zařízení s Windows 10 bude [zaregistrovaný](overview.md#azure-ad-registered-devices) s Azure AD. Pokud vyberete **žádný**, nejsou povoleny zařízení k registraci ve službě Azure AD. Registrace pomocí Microsoft Intune nebo Správa mobilních zařízení (MDM) pro Office 365 vyžaduje registrace. Pokud jste nakonfigurovali některou z těchto služeb **všechny** je vybraná a **NONE** není k dispozici.
- **Vyžadovat Vícefaktorové ověřování pro připojení zařízení** – můžete zvolit, jestli musí uživatelé provést poskytnout druhý ověřovací faktor k [spojení](overview.md#azure-ad-joined-devices) své zařízení do služby Azure AD. Výchozí hodnota je **ne**. Doporučujeme, abyste vyžadování vícefaktorového ověřování při registraci zařízení. Před povolením služby Multi-Factor authentication pro tuto službu, musíte zajistit, že je, že ověřování službou Multi-Factor Authentication nakonfigurováno pro uživatele, které registrují svá zařízení. Další informace o službách různé služby Azure Multi-Factor authentication, naleznete v tématu [Začínáme se službou Azure Multi-Factor authentication](../authentication/concept-mfa-whichversion.md). 

>[!NOTE]
> **Vyžadovat Vícefaktorové ověřování pro připojení zařízení** nastavení se nedá použít u hybridních zařízení připojených k Azure AD.

- **Maximální počet zařízení** – toto nastavení umožňuje vybrat maximální počet zařízení, která uživatel může mít ve službě Azure AD. Pokud uživatel dosáhne této kvóty, jejich nejsou možné přidat další zařízení do jednoho nebo více stávající zařízení se odeberou. Kvóty zařízení se bude počítat pro všechna zařízení, která jsou připojená k Azure AD nebo Azure AD registrované ještě dnes. Výchozí hodnota je **20**.

>[!NOTE]
> **Maximální počet zařízení** nastavení se nedá použít u hybridních zařízení připojených k Azure AD.

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

Pro některá zařízení s Iosem můžete použít názvy zařízení obsahující apostrofy potenciálně různých znaků, které vypadají jako apostrofy. Vyhledávání těchto zařízení je trochu složité – Pokud se nezobrazují výsledky hledání správně, ujistěte se, že hledaný řetězec obsahuje odpovídající znak apostrofu.

## <a name="device-identity-management-tasks"></a>Úkoly správy identity zařízení

Jako globální správce nebo správce cloudových zařízení můžete spravovat zařízení registrovaná nebo připojené k doméně. Správci služby Intune můžete:

- Aktualizace zařízení – příklady jsou každodenní operace, jako je povolení/zakázání zařízení
- Odstranit zařízení – když se zařízení vyřadí z provozu a měla by být odstraněna ve službě Azure AD

Tato část obsahuje informace o běžných úloh správy identity zařízení.

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

- Musíte být globálním správcem nebo cloudové zařízení správce ve službě Azure AD pro povolení / zákaz zařízení. 
- Zakázáním zařízení zabrání zařízení v úspěšném ověření pomocí Azure AD, tím zabrání zařízení v přístupu k prostředkům Azure AD, které jsou chráněné pomocí zařízení certifikační Autority nebo pomocí přihlašovacích údajů WH4B.

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

Pokud je zařízení spravováno nástrojem jinou autoritu pro správu (například Microsoft Intune), ujistěte se, že má zařízení dojde k vymazání / vyřazení před odstraněním tohoto zařízení ve službě Azure AD.

### <a name="view-or-copy-device-id"></a>Zobrazení nebo zkopírovat ID zařízení

ID zařízení můžete ověřit ID podrobností o zařízení na zařízení nebo při řešení potíží s použitím prostředí PowerShell. Pro přístup k možnosti kopírování, klikněte na zařízení.

![Zobrazit ID zařízení](./media/device-management-azure-portal/35.png)
  
### <a name="view-or-copy-bitlocker-keys"></a>Zobrazení nebo kopie klíče Bitlockeru

Můžete zobrazení a zkopírování klíče Bitlockeru k poskytování pomoci uživatelům obnovit svá šifrované jednotce. Tyto klíče jsou dostupné jenom pro zařízení Windows, které jsou šifrované a jejich klíče uloženého ve službě Azure AD. Při přístupu k podrobnosti o zařízení, můžete zkopírovat tyto klíče.

![Zobrazovat klíče Bitlockeru](./media/device-management-azure-portal/36.png)

k zobrazení nebo zkopírování klíče nástroje BitLocker, musíte být vlastníkem zařízení nebo uživatel, který má alespoň jednu z následujících rolí přiřadit:

- Globální správce
- Správce technické podpory
- Správce zabezpečení
- Čtenář zabezpečení
- Správce služby Intune

> [!NOTE]
> Zařízení s Windows 10 připojená k hybridní službě Azure AD nemá vlastníka. Ano Pokud hledáte zařízení podle vlastníka a nebyl nalezen, vyhledávání podle ID zařízení.

## <a name="audit-logs"></a>Protokoly auditu

Aktivity zařízení jsou k dispozici prostřednictvím protokolů aktivit. Tyto protokoly zahrnují aktivity, které jsou aktivované služby registrace zařízení a uživatelů:

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

- Category
- Typ prostředku aktivity
- Aktivita
- Rozsah dat
- Target
- Iniciátor (Actor)

Kromě filtrů můžete vyhledat specifické položky.

![Protokoly auditu](./media/device-management-azure-portal/65.png)

## <a name="next-steps"></a>Další postup

[Správa zastaralých zařízení ve službě Azure AD](manage-stale-devices.md)
