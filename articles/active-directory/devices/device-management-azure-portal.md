---
title: Správa zařízení pomocí Azure Portal | Microsoft Docs
description: Naučte se používat Azure Portal ke správě zařízení.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: conceptual
ms.date: 06/04/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: jairoc
ms.collection: M365-identity-device-management
ms.openlocfilehash: b1b44a7df499dc0b6ce4370bf74ece35c99a4493
ms.sourcegitcommit: fa5ce8924930f56bcac17f6c2a359c1a5b9660c9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/31/2019
ms.locfileid: "73200242"
---
# <a name="manage-device-identities-using-the-azure-portal"></a>Správa identit zařízení pomocí Azure Portal

Správa identit zařízení v Azure Active Directory (Azure AD) vám umožní zajistit, aby vaši uživatelé měli přístup k vašim prostředkům ze zařízení, která splňují vaše standardy pro zabezpečení a dodržování předpisů.

Tento článek:

- Předpokládá, že jste obeznámeni se [seznámkou se správou identit zařízení v Azure Active Directory](overview.md) .
- Poskytuje informace o správě identit zařízení pomocí portálu Azure AD.

## <a name="manage-device-identities"></a>Správa identit zařízení

Portál Azure AD poskytuje centrální místo pro správu identit zařízení. K tomuto umístění se můžete dostat buď pomocí [přímého odkazu](https://portal.azure.com/#blade/Microsoft_AAD_IAM/DevicesMenuBlade/Devices) , nebo pomocí těchto ručních kroků:

1. Přihlaste se k [Azure Portal](https://portal.azure.com) jako správce.
2. V levém navigačním panelu klikněte na **Active Directory**.

   ![Konfigurovat nastavení zařízení](./media/device-management-azure-portal/01.png)

3. V části **Spravovat** klikněte na **zařízení**.

   ![Konfigurovat nastavení zařízení](./media/device-management-azure-portal/74.png)

Stránka **zařízení** vám umožní:

- Konfigurace nastavení zařízení
- Najít zařízení
- Provádění úloh správy identit zařízení
- Kontrola protokolů auditu souvisejících se zařízením  
  
## <a name="configure-device-settings"></a>Konfigurovat nastavení zařízení

Aby bylo možné spravovat identity zařízení pomocí portálu Azure AD, musí být vaše zařízení buď [zaregistrovaná, nebo připojená](overview.md) k Azure AD. Jako správce můžete doladit proces registrace a připojení zařízení konfigurací nastavení zařízení.

![Konfigurovat nastavení zařízení](./media/device-management-azure-portal/22.png)

Stránka nastavení zařízení vám umožní nakonfigurovat tyto možnosti:

![Správa zařízení v Intune](./media/device-management-azure-portal/21.png)

- **Uživatelé můžou připojovat zařízení do Azure AD** – toto nastavení umožňuje vybrat uživatele, kteří můžou svá zařízení zaregistrovat jako zařízení připojená k Azure AD. Výchozí hodnota je **All**.

> [!NOTE]
> **Uživatelé můžou připojovat zařízení k nastavení Azure AD** jenom pro službu Azure AD JOIN ve Windows 10.

- **Další místní správci na zařízeních připojených k Azure AD** – můžete vybrat uživatele, kterým se v zařízení udělují oprávnění místního správce. Sem přidaní uživatelé se přidají do role *Správci zařízení* v Azure AD. Globální správci ve službě Azure AD a vlastníci zařízení mají ve výchozím nastavení udělena práva místního správce. Tato možnost je funkce Premium Edition, která je dostupná prostřednictvím produktů, jako je Azure AD Premium nebo sada Enterprise Mobility Suite (EMS).
- **Uživatelé mohou zaregistrovat svá zařízení ve službě Azure AD** – je třeba nakonfigurovat toto nastavení tak, aby bylo možné zaregistrovat zařízení s Windows 10 osobní, iOS, Androidem a macOs, která budou registrována ve službě Azure AD. Pokud vyberete možnost **žádné**, zařízení se nemůžou registrovat v Azure AD. Registrace pomocí Microsoft Intune nebo správy mobilních zařízení (MDM) pro Office 365 vyžaduje registraci. Pokud jste nakonfigurovali některou z těchto služeb, je vybraná možnost **vše** a **žádná** není k dispozici.
- **Vyžadovat vícefaktorové ověřování pro připojení zařízení** – můžete si vybrat, jestli uživatelé musí poskytovat dodatečný ověřovací faktor k připojení zařízení k Azure AD. Výchozí hodnota je **ne**. Při registraci zařízení doporučujeme vyžadovat službu Multi-Factor Authentication. Než povolíte službu Multi-Factor Authentication pro tuto službu, musíte zajistit, aby bylo pro uživatele, kteří registrují svá zařízení, nakonfigurované vícefaktorové ověřování. Další informace o různých službách Azure Multi-Factor Authentication najdete v tématu [Začínáme se službou Azure Multi-Factor Authentication](../authentication/concept-mfa-whichversion.md). 

> [!NOTE]
> **Vyžadovat použití vícefaktorového ověřování pro připojení zařízení** se vztahuje na zařízení, která jsou buď připojená k Azure AD, nebo v případě registrace Azure AD. Toto nastavení se nevztahuje na zařízení připojená k hybridní službě Azure AD.

- **Maximální počet zařízení** – toto nastavení umožňuje vybrat maximální počet připojených zařízení Azure AD nebo zařízení s registrovanými službou Azure AD, která může uživatel mít ve službě Azure AD. Pokud uživatel dosáhne této kvóty, nebude moct přidávat další zařízení, dokud nebudou odebrána některá z existujících zařízení. Výchozí hodnota je **20**.

> [!NOTE]
> Nastavení **maximální počet zařízení** se vztahuje na zařízení, která jsou buď připojená ke službě Azure AD, nebo v zaregistrovaných Azure AD. Toto nastavení se nevztahuje na zařízení připojená k hybridní službě Azure AD.

- **Uživatelé můžou synchronizovat nastavení a data aplikací napříč zařízeními** – ve výchozím nastavení je toto nastavení nastaveno na **žádné**. Když vyberete konkrétní uživatele nebo skupiny, umožníte synchronizaci dat uživatelů a aplikací v zařízeních s Windows 10. Přečtěte si další informace o tom, jak synchronizace funguje ve Windows 10.
Tato možnost je prémiová funkce dostupná prostřednictvím produktů, jako je Azure AD Premium nebo sada Enterprise Mobility Suite (EMS).

## <a name="locate-devices"></a>Najít zařízení

Máte dvě možnosti, jak vyhledat registrovaná a připojená zařízení:

- **Všechna zařízení** v části **Spravovat** na stránce **zařízení**  

   ![Všechna zařízení](./media/device-management-azure-portal/41.png)

- **Zařízení** v části **Spravovat** na stránce **uživatele**

   ![Všechna zařízení](./media/device-management-azure-portal/43.png)

Pomocí obou možností se můžete dostat k zobrazení, které:

- Umožňuje hledat zařízení pomocí zobrazovaného jména nebo ID zařízení jako filtru.
- Poskytuje podrobný přehled registrovaných a připojených zařízení.
- Umožňuje provádět běžné úlohy správy zařízení.

![Všechna zařízení](./media/device-management-azure-portal/51.png)

>[!TIP]
>
>* Pokud se v zaregistrovaném sloupci zobrazí zařízení, které je připojené k hybridní službě Azure AD se stavem "čeká", znamená to, že zařízení bylo synchronizované z Azure AD Connect a čeká na dokončení registrace z klienta. Přečtěte si další informace o [Naplánování hybridní implementace služby Azure AD JOIN](hybrid-azuread-join-plan.md). Další informace najdete v článku o [nejčastějších](faq.md)dotazech k zařízení.
>
>   ![Zařízení čekají](./media/device-management-azure-portal/75.png)
>
>* U některých zařízení s iOS můžou názvy zařízení, které obsahují apostrofy, potenciálně používat různé znaky, které vypadají jako apostrofy. Takže hledání těchto zařízení je trochu obtížné – Pokud nevidíte výsledky hledání správně, ujistěte se, že hledaný řetězec obsahuje znak, který se shoduje s apostrofem.

## <a name="device-identity-management-tasks"></a>Úlohy správy identit zařízení

Jako globální správce nebo správce cloudového zařízení můžete spravovat registrovaná nebo připojená zařízení. Správci služby Intune můžou:

- Aktualizace zařízení – příklady jsou každodenní operace, jako je povolování nebo zakazování zařízení.
- Odstranění zařízení – při vyřazení zařízení z provozu a jeho odstranění ve službě Azure AD

V této části najdete informace o běžných úlohách správy identit zařízení.

### <a name="manage-an-intune-device"></a>Správa zařízení v Intune

Pokud jste správce Intune, můžete zařízení s označením **Microsoft Intune**spravovat. Pokud zařízení není zaregistrované v Microsoft Intune možnost spravovat bude šedá.

![Správa zařízení v Intune](./media/device-management-azure-portal/31.png)

### <a name="enable--disable-an-azure-ad-device"></a>Povolení nebo zakázání zařízení Azure AD

Pokud chcete zařízení povolit nebo zakázat, máte dvě možnosti:

- Nabídka úkoly ("...") na stránce **všechna zařízení**

   ![Správa zařízení v Intune](./media/device-management-azure-portal/71.png)

- Panel nástrojů na stránce **zařízení**

   ![Správa zařízení v Intune](./media/device-management-azure-portal/32.png)

**Mark**

- Aby bylo možné zařízení povolit nebo zakázat, musíte být ve službě Azure AD globálním správcem nebo správcem cloudového zařízení. 
- Vypnutí zařízení brání úspěšnému ověření zařízení ve službě Azure AD, takže zařízení nebude mít přístup k prostředkům služby Azure AD, které jsou chráněné pomocí certifikační autority zařízení nebo pomocí přihlašovacích údajů WH4B.
- Zakázáním zařízení se na zařízení odvolá primární obnovovací token (PRT) a všechny obnovovací tokeny (RT).

### <a name="delete-an-azure-ad-device"></a>Odstranění zařízení Azure AD

Pokud chcete odstranit zařízení, máte dvě možnosti:

- Nabídka úkoly ("...") na stránce **všechna zařízení**

   ![Správa zařízení v Intune](./media/device-management-azure-portal/72.png)

- Panel nástrojů na stránce **zařízení**

   ![Odstranění zařízení](./media/device-management-azure-portal/34.png)

**Mark**

- Aby bylo možné odstranit zařízení, musíte být globálním správcem nebo správcem služby Intune ve službě Azure AD.
- Odstraňování zařízení:
   - Zabrání zařízení v přístupu k prostředkům služby Azure AD.
   - Odebere všechny podrobnosti, které jsou připojené k zařízení, například klíče BitLockeru pro zařízení s Windows.  
   - Představuje neobnovitelná aktivitu a nedoporučuje se, pokud to není nutné.

Pokud je zařízení spravované jinou autoritou pro správu (například Microsoft Intune), ujistěte se, že zařízení bylo smazáno/vyřazeno před odstraněním zařízení v Azure AD. Před odstraněním všech zařízení si přečtěte, jak [Spravovat zastaralá zařízení](device-management-azure-portal.md) .

### <a name="view-or-copy-device-id"></a>Zobrazení nebo kopírování ID zařízení

ID zařízení můžete použít k ověření podrobností ID zařízení v zařízení nebo při řešení potíží pomocí prostředí PowerShell. Pokud chcete získat přístup k možnosti kopírování, klikněte na zařízení.

![Zobrazit ID zařízení](./media/device-management-azure-portal/35.png)
  
### <a name="view-or-copy-bitlocker-keys"></a>Zobrazení nebo kopírování klíčů nástroje BitLocker

Můžete zobrazit a zkopírovat klíče nástroje BitLocker, které uživatelům pomůžou obnovit zašifrovanou jednotku. Tyto klíče jsou k dispozici pouze pro zařízení s Windows, která jsou zašifrovaná a mají své klíče uložené v Azure AD. Tyto klíče můžete zkopírovat při přístupu k podrobnostem o zařízení.

![Zobrazit klíče BitLockeru](./media/device-management-azure-portal/36.png)

Chcete-li zobrazit nebo zkopírovat klíče nástroje BitLocker, musíte být buď vlastníkem zařízení, nebo uživatelem, který má přiřazenu alespoň jednu z následujících rolí:

- Správce cloudového zařízení
- Globální správce
- Správce helpdesku
- Správce služby Intune
- Správce zabezpečení
- Čtecí modul zabezpečení

> [!NOTE]
> Hybridní zařízení s Windows 10 připojená k Azure AD nemají vlastníka. Takže pokud hledáte zařízení podle vlastníka a nenalezli jste ho, vyhledejte ID zařízení.

## <a name="audit-logs"></a>Protokoly auditu

Aktivity zařízení jsou k dispozici prostřednictvím protokolů aktivit. Tyto protokoly zahrnují aktivity aktivované službou Device Registration Service a uživateli:

- Vytvoření zařízení a přidání vlastníků/uživatelů na zařízení
- Změny nastavení zařízení
- Operace zařízení, jako je například odstranění nebo aktualizace zařízení

Vstupním bodem pro data auditování jsou **protokoly auditu** v části **aktivita** stránky **zařízení** .

![Protokoly auditu](./media/device-management-azure-portal/61.png)

Protokol auditu má výchozí zobrazení seznamu, které obsahuje následující položky:

- Datum a čas výskytu
- Cíle
- Iniciátor/actor (kdo) aktivity
- Aktivita (co)

![Protokoly auditu](./media/device-management-azure-portal/63.png)

Zobrazení seznamu můžete upravit kliknutím na **Sloupce** na panelu nástrojů.

![Protokoly auditu](./media/device-management-azure-portal/64.png)

Abyste omezili zobrazovaná data na úroveň, která vám vyhovuje, můžete filtrovat data přihlašování s využitím následujících polí:

- Kategorie
- Typ prostředku aktivity
- Aktivita
- Rozsah dat
- Výběr cílového umístění
- Iniciované uživatelem (actor)

Kromě filtrů můžete vyhledat konkrétní položky.

![Protokoly auditu](./media/device-management-azure-portal/65.png)

## <a name="next-steps"></a>Další kroky

[Jak spravovat zastaralá zařízení ve službě Azure AD](manage-stale-devices.md)
