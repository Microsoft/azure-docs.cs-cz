---
title: Správa zařízení pomocí Azure Portal | Microsoft Docs
description: Naučte se používat Azure Portal ke správě zařízení.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: how-to
ms.date: 09/16/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: hafowler
ms.collection: M365-identity-device-management
ms.openlocfilehash: 92a0cc93b4d159a4ba87c1cadc2d0dedc0a28b2d
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/22/2021
ms.locfileid: "98683806"
---
# <a name="manage-device-identities-using-the-azure-portal"></a>Správa identit zařízení přes Azure Portal

Azure AD poskytuje centrální místo pro správu identit zařízení.

Stránka **všechna zařízení** vám umožní:

- Identifikujte zařízení, včetně:
   - Zařízení, která byla připojena nebo registrována v Azure AD.
   - Zařízení nasazená pomocí [Windows autopilotu](/windows/deployment/windows-autopilot/windows-autopilot)
   - Tiskárny využívající [univerzální tisk](/universal-print/fundamentals/universal-print-getting-started)
- Proveďte úlohy správy identit zařízení, jako je například povolit, zakázat, odstranit nebo spravovat.
   - [Tiskárny](/universal-print/fundamentals/) a zařízení [Windows autopilotu](/windows/deployment/windows-autopilot/windows-autopilot) mají v Azure AD omezené možnosti správy. Musí být spravovány ze svých příslušných rozhraní správce.
- Nakonfigurujte nastavení identity zařízení.
- Povolí nebo zakáže Enterprise State Roaming.
- Kontrola protokolů auditu souvisejících se zařízením

[![Zobrazení všech zařízení v Azure Portal](./media/device-management-azure-portal/all-devices-azure-portal.png)](./media/device-management-azure-portal/all-devices-azure-portal.png#lightbox)

Přístup k portálu zařízení získáte pomocí následujících kroků:

1. Přihlaste se na [Azure Portal](https://portal.azure.com).
1. Přejděte na **Azure Active Directory**  >  **zařízení**.

## <a name="manage-devices"></a>Správa zařízení

Pro správu zařízení ve službě Azure AD existují dvě místa:

- **Azure Portal**  >  **Azure Active Directory**  >  **Zařízení**
- **Azure Portal**  >  **Azure Active Directory**  >  **Uživatelé** > vybrat **zařízení** > uživatele

Obě možnosti umožňují správcům:

- Vyhledejte zařízení.
- Podívejte se na podrobnosti o zařízení, včetně:
    - Název zařízení
    - ID zařízení
    - Operační systém a verze
    - Typ spojení
    - Vlastník
    - Správa mobilních zařízení a dodržování předpisů
    - Obnovovací klíč nástroje BitLocker
- Proveďte úlohy správy identit zařízení, jako je, povolit, zakázat, odstranit nebo spravovat.
   - [Tiskárny](/universal-print/fundamentals/) a zařízení [Windows autopilotu](/windows/deployment/windows-autopilot/windows-autopilot) mají v Azure AD omezené možnosti správy. Musí být spravovány ze svých příslušných rozhraní správce.

> [!TIP]
> - Hybridní zařízení s Windows 10 připojená k Azure AD nemají vlastníka. Pokud hledáte zařízení podle vlastníka a nenalezli jste ho, vyhledejte ID zařízení.
>
> - Pokud se v zaregistrovaném sloupci zobrazí zařízení, které je připojené k hybridní službě Azure AD se stavem "čeká", znamená to, že zařízení bylo synchronizované z Azure AD Connect a čeká na dokončení registrace z klienta. Přečtěte si další informace o [Naplánování hybridní implementace služby Azure AD JOIN](hybrid-azuread-join-plan.md). Další informace najdete v článku o [nejčastějších](faq.md)dotazech k zařízení.
>
> - U některých zařízení s iOS můžou názvy zařízení, které obsahují apostrofy, potenciálně používat různé znaky, které vypadají jako apostrofy. Takže hledání těchto zařízení je trochu obtížné – Pokud nevidíte výsledky hledání správně, ujistěte se, že hledaný řetězec obsahuje znak, který se shoduje s apostrofem.

### <a name="manage-an-intune-device"></a>Správa zařízení v Intune

Pokud jste správce Intune, můžete spravovat zařízení, ve kterých je MDM označená **Microsoft Intune**. Pokud zařízení není zaregistrované v Microsoft Intune, bude možnost spravovat šedá.

### <a name="enable-or-disable-an-azure-ad-device"></a>Povolení nebo zakázání zařízení Azure AD

Pokud chcete zařízení povolit nebo zakázat, máte dvě možnosti:

- Panel nástrojů na stránce **všechna zařízení** po výběru jednoho nebo více zařízení.
- Panel nástrojů po přechodu k podrobnostem určitého zařízení.

> [!IMPORTANT]
> - Abyste mohli zařízení povolit nebo zakázat, musíte být ve službě Azure AD globálním správcem nebo správcem cloudového zařízení. 
> - Zakázáním zařízení předejdete úspěšnému ověřování zařízení ve službě Azure AD, takže zařízení nebude mít přístup k prostředkům Azure AD chráněným pomocí podmíněného přístupu na základě zařízení nebo pomocí přihlašovacích údajů Windows Hello pro firmy.
> - Zakázáním zařízení se na zařízení odvolá primární obnovovací token (PRT) a všechny obnovovací tokeny (RT).
> - V Azure AD nelze povolit nebo zakázat tiskárny.

### <a name="delete-an-azure-ad-device"></a>Odstranění zařízení Azure AD

Pokud chcete odstranit zařízení, máte dvě možnosti:

- Panel nástrojů na stránce **všechna zařízení** po výběru jednoho nebo více zařízení.
- Panel nástrojů po přechodu k podrobnostem určitého zařízení.

> [!IMPORTANT]
> - Aby bylo možné odstranit zařízení, musíte mít přiřazenou roli správce cloudového zařízení, správce Intune nebo globální správce v Azure AD.
> - V Azure AD nejde odstranit tiskárny a zařízení Windows autopilotu.
> - Odstraňování zařízení:
>    - Zabrání zařízení v přístupu k prostředkům služby Azure AD.
>    - Odebere všechny podrobnosti, které jsou připojené k zařízení, například klíče BitLockeru pro zařízení s Windows.  
>    - Představuje neobnovitelná aktivitu a nedoporučuje se, pokud to není nutné.

Pokud je zařízení spravované jinou autoritou pro správu (například Microsoft Intune), ujistěte se, že zařízení bylo smazáno/vyřazeno před odstraněním zařízení v Azure AD. Před odstraněním všech zařízení si přečtěte, jak [Spravovat zastaralá zařízení](manage-stale-devices.md) .

### <a name="view-or-copy-device-id"></a>Zobrazení nebo kopírování ID zařízení

ID zařízení můžete použít k ověření podrobností ID zařízení v zařízení nebo při řešení potíží pomocí prostředí PowerShell. Pokud chcete získat přístup k možnosti kopírování, klikněte na zařízení.

![Zobrazit ID zařízení](./media/device-management-azure-portal/35.png)
  
### <a name="view-or-copy-bitlocker-keys"></a>Zobrazení nebo kopírování klíčů nástroje BitLocker

Klíče nástroje BitLocker můžete zobrazit a zkopírovat a uživatelům tak dovolit obnovení šifrovaných jednotek. Tyto klíče jsou k dispozici pouze pro zařízení s Windows, která jsou zašifrovaná a mají své klíče uložené v Azure AD. Tyto klíče můžete najít při přístupu k podrobnostem o zařízení výběrem možnosti **Zobrazit obnovovací klíč**. Když vyberete **Zobrazit obnovovací klíč** , vygeneruje se protokol auditu, který najdete v `KeyManagement` kategorii.

![Zobrazit klíče BitLockeru](./media/device-management-azure-portal/device-details-show-bitlocker-key.png)

Chcete-li zobrazit nebo zkopírovat klíče nástroje BitLocker, musíte být buď vlastníkem zařízení, nebo uživatelem, který má přiřazenu alespoň jednu z následujících rolí:

- Správce cloudového zařízení
- Globální správce
- Správce helpdesku
- Správce služby Intune
- Správce zabezpečení
- Čtenář zabezpečení

### <a name="device-list-filtering-preview"></a>Filtrování seznamu zařízení (Preview)

Dřív jste mohli filtrovat jenom seznam zařízení podle aktivity a povoleného stavu. Tato verze Preview teď umožňuje filtrovat seznam zařízení podle následujících atributů na zařízení:

- Povolený stav
- Stav kompatibility
- Typ spojení (připojeno k Azure AD, připojeno k hybridní službě Azure AD, zaregistrovaný Azure AD)
- Časové razítko aktivity
- Operační systém
- Typ zařízení (tiskárny, zabezpečené virtuální počítače, sdílená zařízení, registrovaná zařízení)

Povolení funkce filtrování ve verzi Preview v zobrazení **všechna zařízení** :

![Povolit funkci Preview pro filtrování](./media/device-management-azure-portal/device-filter-preview-enable.png)

1. Přihlaste se na [Azure Portal](https://portal.azure.com).
1. Přejděte na **Azure Active Directory**  >  **zařízení**.
1. Vyberte banner, který uvádí, a **Vyzkoušejte si nová vylepšení filtrování zařízení. Kliknutím povolíte náhled.**

Teď budete mít možnost **Přidat filtry** do zobrazení **všechna zařízení** .

## <a name="configure-device-settings"></a>Konfigurace nastavení zařízení

Pokud chcete spravovat identity zařízení pomocí portálu Azure AD, musí být tato zařízení [zaregistrovaná nebo připojená](overview.md) k Azure AD. Jako správce můžete řídit proces registrace a připojení zařízení pomocí konfigurace následujících nastavení zařízení.

Chcete-li zobrazit nebo spravovat nastavení zařízení v Azure Portal, je nutné přiřadit jednu z následujících rolí:

- Globální správce
- Správce cloudového zařízení
- Globální čtenář
- Čtečka adresářů

![Nastavení zařízení související s Azure AD](./media/device-management-azure-portal/device-settings-azure-portal.png)

- **Uživatelé můžou připojovat zařízení do Azure AD** – toto nastavení umožňuje vybrat uživatele, kteří můžou svá zařízení zaregistrovat jako zařízení připojená k Azure AD. Výchozí hodnota je **All**.

> [!NOTE]
> **Uživatelé můžou připojovat zařízení k nastavení Azure AD** jenom pro službu Azure AD JOIN ve Windows 10.

- **Další místní správci na zařízeních připojených k Azure AD** – můžete vybrat uživatele, kterým se v zařízení udělují oprávnění místního správce. Tito uživatelé se přidají do role *Správci zařízení* v Azure AD. Globální správci ve službě Azure AD a vlastníci zařízení mají ve výchozím nastavení udělena práva místního správce. Tato možnost je funkce Premium Edition, která je dostupná prostřednictvím produktů, jako je Azure AD Premium nebo sada Enterprise Mobility Suite (EMS).
- **Uživatelé mohou zaregistrovat svá zařízení ve službě Azure AD** – je třeba nakonfigurovat toto nastavení tak, aby bylo možné zaregistrovat zařízení s Windows 10 osobní, iOS, Androidem a MacOS, která budou registrována ve službě Azure AD. Pokud vyberete možnost **žádné**, zařízení se nemůžou registrovat v Azure AD. Registrace pomocí Microsoft Intune nebo správy mobilních zařízení (MDM) pro Microsoft 365 vyžaduje registraci. Pokud jste nakonfigurovali některou z těchto služeb, je vybraná možnost **vše** a **žádná** není k dispozici.
- **Zařízení, která mají být připojená ke službě Azure AD nebo registrovaná služba Azure AD, vyžadují Multi-Factor Authentication** – můžete si vybrat, jestli uživatelé musí poskytovat dodatečný ověřovací faktor k připojení nebo registraci zařízení ke službě Azure AD. Výchozí hodnota je **ne**. Při registraci nebo připojení k zařízení doporučujeme vyžadovat službu Multi-Factor Authentication. Než povolíte službu Multi-Factor Authentication pro tuto službu, musíte zajistit, aby bylo pro uživatele, kteří registrují svá zařízení, nakonfigurované vícefaktorové ověřování. Další informace o různých Multi-Factor Authentication služby Azure AD najdete v tématu [Začínáme se službou Azure ad Multi-Factor Authentication](../authentication/concept-mfa-howitworks.md). 

> [!NOTE]
> Zařízení, která mají **být připojená k Azure AD nebo jsou registrovaná služba Azure AD, vyžadují Multi-Factor Authentication** nastavení platí pro zařízení, která jsou připojená buď ke službě Azure AD (s některými výjimkami), nebo s registrovanou službou Toto nastavení se nevztahuje na hybridní zařízení připojená k Azure AD, [připojené virtuální počítače Azure AD na](./howto-vm-sign-in-azure-ad-windows.md#enabling-azure-ad-login-in-for-windows-vm-in-azure) zařízeních s Azure a připojená k Azure AD pomocí [režimu automatického nasazení Windows autopilotu](/mem/autopilot/self-deploying).

- **Maximální počet zařízení** – toto nastavení umožňuje vybrat maximální počet připojených zařízení Azure AD nebo zařízení s registrovanými službou Azure AD, která může uživatel mít ve službě Azure AD. Pokud uživatel dosáhne této kvóty, nebude moct přidávat další zařízení, dokud nebudou odebrána některá z existujících zařízení. Výchozí hodnota je **50**.

> [!NOTE]
> Nastavení **maximální počet zařízení** se vztahuje na zařízení, která jsou buď připojená ke službě Azure AD, nebo v zaregistrovaných Azure AD. Toto nastavení se nevztahuje na zařízení připojená k hybridní službě Azure AD.

- [Enterprise State Roaming](enterprise-state-roaming-overview.md)

## <a name="audit-logs"></a>Protokoly auditu

Aktivity zařízení jsou k dispozici prostřednictvím protokolů aktivit. Tyto protokoly zahrnují aktivity aktivované službou Device Registration Service a uživateli:

- Vytvoření zařízení a přidání vlastníků/uživatelů na zařízení
- Změny nastavení zařízení
- Operace zařízení, jako je například odstranění nebo aktualizace zařízení

Vstupním bodem pro data auditování jsou **protokoly auditu** v části **aktivita** stránky **zařízení** .

Protokol auditu má výchozí zobrazení seznamu, které obsahuje:

- Datum a čas výskytu
- Cíle
- Iniciátor/actor (kdo) aktivity
- Aktivita (co)

:::image type="content" source="./media/device-management-azure-portal/63.png" alt-text="Snímek obrazovky tabulky v části aktivita na stránce zařízení, kde jsou uvedeny datum, cíl, objekt actor a aktivita pro čtyři protokoly auditu." border="false":::

Zobrazení seznamu můžete upravit kliknutím na **Sloupce** na panelu nástrojů.

:::image type="content" source="./media/device-management-azure-portal/64.png" alt-text="Snímek obrazovky se stránkou nástrojů zařízení. Položka sloupce je zvýrazněna." border="false":::

Abyste omezili zobrazovaná data na úroveň, která vám vyhovuje, můžete filtrovat data přihlašování s využitím následujících polí:

- Kategorie
- Typ prostředku aktivity
- Aktivita
- Rozsah dat
- Cíl
- Iniciované uživatelem (actor)

Kromě filtrů můžete vyhledat konkrétní položky.

:::image type="content" source="./media/device-management-azure-portal/65.png" alt-text="Snímek obrazovky s ovládacími prvky filtru dat pro audit s kategoriemi, typem prostředku aktivity, aktivitou, rozsahem data a polem actor a vyhledávacím polem." border="false":::

## <a name="next-steps"></a>Další kroky

[Jak spravovat zastaralá zařízení ve službě Azure AD](manage-stale-devices.md)

[Enterprise State Roaming](enterprise-state-roaming-overview.md)