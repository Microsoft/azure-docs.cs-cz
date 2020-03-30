---
title: Jak spravovat zařízení pomocí portálu Azure | Dokumenty společnosti Microsoft
description: Přečtěte si, jak pomocí portálu Azure spravovat zařízení.
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
ms.openlocfilehash: e09de5911ca0946bfcbcb77d1ad4131c8feac9f0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79262240"
---
# <a name="manage-device-identities-using-the-azure-portal"></a>Správa identit zařízení pomocí portálu Azure

Díky správě identit zařízení ve službě Azure Active Directory (Azure AD) můžete zajistit, aby vaši uživatelé přistupovali k vašim prostředkům ze zařízení, která splňují vaše standardy zabezpečení a dodržování předpisů.

Tento článek:

- Předpokládá, že jste obeznámeni s [úvodem ke správě identit zařízení ve službě Azure Active Directory.](overview.md)
- Poskytuje informace o správě identit zařízení pomocí portálu Azure AD.

## <a name="manage-device-identities"></a>Správa identit zařízení

Portál Azure AD poskytuje centrální místo pro správu identit zařízení. Na toto místo se můžete dostat buď pomocí [přímého odkazu,](https://portal.azure.com/#blade/Microsoft_AAD_IAM/DevicesMenuBlade/Devices) nebo:

1. Přihlaste se k [portálu Azure](https://portal.azure.com).
1. Přejděte na **Azure Active Directory** > **Devices**.

Stránka **Zařízení** umožňuje:

- Konfigurace nastavení zařízení
- Najít zařízení
- Provádění úloh správy identit zařízení
- Kontrola protokolů auditu souvisejících se zařízením  
  
## <a name="configure-device-settings"></a>Konfigurace nastavení zařízení

Chcete-li spravovat identity zařízení pomocí portálu Azure AD, musí být vaše zařízení [zaregistrovaná nebo připojená k](overview.md) Azure AD. Jako správce můžete doladit proces registrace a spojování zařízení konfigurací nastavení zařízení.

Stránka nastavení zařízení umožňuje konfigurovat nastavení související s identitami zařízení:

![Správa zařízení Intune](./media/device-management-azure-portal/21.png)

- **Uživatelé se můžou připojit zařízení k Azure AD** – toto nastavení umožňuje vybrat uživatele, kteří můžete zaregistrovat svá zařízení jako zařízení připojená k Azure AD. Výchozí hodnota je **Vše**.

> [!NOTE]
> **Uživatelé se mohou připojit zařízení k** nastavení Azure AD je pouze pro připojení Azure AD na Windows 10.

- **Další místní správci na zařízeních spojených s Azure AD** – můžete vybrat uživatele, kterým jsou na zařízení udělena práva místního správce. Uživatelé, kteří jsou sem přidáni, se přidají do role *Správci zařízení* ve službě Azure AD. Globální správci ve službě Azure AD a vlastníci zařízení mají ve výchozím nastavení udělena práva místního správce. Tato možnost je funkce prémiové edice dostupná prostřednictvím produktů, jako je Azure AD Premium nebo Enterprise Mobility Suite (EMS).
- **Uživatelé mohou zaregistrovat svá zařízení ve službě Azure AD** – toto nastavení je potřeba nakonfigurovat tak, aby se zařízení s Windows 10 pro osobní, iOS, Android a macOs mohla zaregistrovat ve službě Azure AD. Pokud vyberete **žádné**, zařízení nejsou povoleny k registraci s Azure AD. Registrace pomocí Microsoft Intune nebo Správy mobilních zařízení (MDM) pro Office 365 vyžaduje registraci. Pokud jste nakonfigurovali některou z těchto služeb, je vybrána možnost **ALL** a **možnost ŽÁDNÝ** není k dispozici.
- **Vyžadovat vícefaktorové ověření pro připojení zařízení** – můžete si vybrat, zda uživatelé jsou povinni poskytnout další faktor ověřování připojit své zařízení k Azure AD. Výchozí hodnota je **Ne**. Doporučujeme vyžadovat vícefaktorové ověřování při registraci zařízení. Před povolením vícefaktorového ověřování pro tuto službu je nutné zajistit, aby bylo vícefaktorové ověřování nakonfigurováno pro uživatele, kteří registrují svá zařízení. Další informace o různých službách azure vícefaktorového ověřování najdete [v tématu Začínáme s vícefaktorovým ověřováním Azure](../authentication/concept-mfa-whichversion.md). 

> [!NOTE]
> **Vyžadovat multi-Factor Auth připojit zařízení** nastavení platí pro zařízení, která jsou buď Azure AD připojen nebo Azure AD registrované. Toto nastavení se nevztahuje na hybridní zařízení připojená k Azure AD.

- **Maximální počet zařízení** – toto nastavení umožňuje vybrat maximální počet zařízení registrovaných pro Azure AD nebo Azure AD registrovaných zařízení, které uživatel může mít ve službě Azure AD. Pokud uživatel dosáhne této kvóty, nebude moci přidat další zařízení, dokud nebude odebráno jedno nebo více existujících zařízení. Výchozí hodnota je **20**.

> [!NOTE]
> **Maximální počet zařízení** nastavení platí pro zařízení, která jsou buď Azure AD připojen nebo Azure AD registrované. Toto nastavení se nevztahuje na hybridní zařízení připojená k Azure AD.

- **Uživatelé mohou synchronizovat nastavení a data aplikací mezi zařízeními** – ve výchozím nastavení je toto nastavení nastaveno na **hodnotu NONE**. Výběr konkrétních uživatelů nebo skupin nebo ALL umožňuje synchronizaci nastavení a dat aplikací uživatele na svých zařízeních s Windows 10. Přečtěte si další informace o tom, jak synchronizace funguje ve Windows 10.
Tato možnost je prémiová funkce dostupná prostřednictvím produktů, jako je Azure AD Premium nebo Enterprise Mobility Suite (EMS).

## <a name="locate-devices"></a>Najít zařízení

Máte dvě možnosti, jak vyhledat registrovaná a připojená zařízení:

- **Všechna zařízení** v části **Spravovat** na stránce **Zařízení**  
- **Zařízení** v části **Spravovat** na stránce **Uživatel**

S oběma možnostmi se můžete dostat k zobrazení, které:

- Umožňuje vyhledávat zařízení pomocí zobrazovacího jména nebo ID zařízení jako filtru.
- Poskytuje podrobný přehled registrovaných a spojených zařízení
- Umožňuje provádět běžné úlohy správy zařízení.

![Všechna zařízení](./media/device-management-azure-portal/51.png)

>[!TIP]
>
>* Pokud se zobrazí zařízení, které je "Hybridní Azure AD připojen" se stavem "Čeká na vyřízení" ve sloupci REGISTERED, znamená to, že zařízení bylo synchronizováno z připojení Azure AD a čeká na dokončení registrace z klienta. Přečtěte si další informace o tom, jak [naplánovat implementaci připojení hybridního Azure AD](hybrid-azuread-join-plan.md). Další informace naleznete v [článku, Zařízení často kladené otázky](faq.md).
>
>   ![Nevyřízená zařízení](./media/device-management-azure-portal/75.png)
>
>* U některých zařízení se systémem iOS mohou názvy zařízení obsahující apostrofy potenciálně používat různé znaky, které vypadají jako apostrofy. Takže hledání takových zařízení je trochu složité - pokud nevidíte výsledky vyhledávání správně, ujistěte se, že vyhledávací řetězec obsahuje odpovídající znak apostrof.

## <a name="device-identity-management-tasks"></a>Úlohy správy identit zařízení

Jako globální správce nebo správce cloudových zařízení můžete spravovat registrovaná nebo připojená zařízení. Správci služby Intune mohou:

- Aktualizační zařízení – příkladem jsou každodenní operace, jako je povolení/zakázání zařízení.
- Odstranění zařízení – když je zařízení vyřazeno a mělo by se odstranit ve službě Azure AD

Tato část obsahuje informace o běžných úlohách správy identit zařízení.

### <a name="manage-an-intune-device"></a>Správa zařízení Intune

Pokud jste správce Intune, můžete spravovat zařízení označená jako **Microsoft Intune**. Pokud zařízení není zaregistrované pomocí Microsoft Intune, bude možnost "Spravovat" zašedlá.

![Správa zařízení Intune](./media/device-management-azure-portal/31.png)

### <a name="enable--disable-an-azure-ad-device"></a>Povolení nebo zakázání zařízení Azure AD

Chcete-li zařízení povolit/ zakázat, máte dvě možnosti:

- Nabídka úloh ("...") na stránce **Všechna zařízení**

   ![Správa zařízení Intune](./media/device-management-azure-portal/71.png)

- Panel nástrojů na stránce **Zařízení**

   ![Správa zařízení Intune](./media/device-management-azure-portal/32.png)

**Poznámky:**

- Chcete-li zařízení povolit nebo zakázat, musíte být globálním správcem nebo správcem cloudových zařízení ve službě Azure AD. 
- Zakázání zařízení zabrání zařízení v úspěšném ověřování pomocí Azure AD, a tím zabrání zařízení v přístupu k prostředkům Azure AD, které jsou chráněny certifikační autoritou zařízení nebo pomocí pověření WH4B.
- Zakázáním zařízení zrušíte primární obnovovací token (PRT) i všechny obnovovací tokeny (RT) v zařízení.

### <a name="delete-an-azure-ad-device"></a>Odstranění zařízení Azure AD

Chcete-li zařízení odstranit, máte dvě možnosti:

- Nabídka úloh ("...") na stránce **Všechna zařízení**

   ![Správa zařízení Intune](./media/device-management-azure-portal/72.png)

- Panel nástrojů na stránce **Zařízení**

   ![Odstranit zařízení](./media/device-management-azure-portal/34.png)

**Poznámky:**

- Abyste zařízení odstranili, musíte být globální správce nebo správce Intune ve službě Azure AD.
- Odstranění zařízení:
   - Zabrání zařízení v přístupu k prostředkům Azure AD.
   - Odebere všechny podrobnosti, které jsou připojeny k zařízení, například klíče nástroje BitLocker pro zařízení se systémem Windows.  
   - Představuje neobnovitelnou aktivitu a nedoporučuje se, pokud není požadováno.

Pokud je zařízení spravované jiným orgánem pro správu (například Microsoft Intune), ujistěte se, že zařízení bylo vymazáno nebo vyřazeno před odstraněním zařízení ve službě Azure AD. Před odstraněním všech zařízení zkontrolujte, jak [spravovat zastaralá zařízení.](device-management-azure-portal.md)

### <a name="view-or-copy-device-id"></a>Zobrazení nebo kopírování ID zařízení

ID zařízení můžete použít k ověření podrobností id zařízení v zařízení nebo pomocí PowerShellu během řešení potíží. Chcete-li získat přístup k možnosti kopírování, klepněte na zařízení.

![Zobrazení ID zařízení](./media/device-management-azure-portal/35.png)
  
### <a name="view-or-copy-bitlocker-keys"></a>Zobrazení nebo kopírování klíčů nástroje BitLocker

Klíče nástroje BitLocker můžete zobrazit a zkopírovat a pomoci tak uživatelům obnovit jejich šifrovanou jednotku. Tyto klíče jsou k dispozici jenom pro zařízení s Windows, která jsou šifrovaná a jejich klíče jsou uložené ve službě Azure AD. Tyto klíče můžete zkopírovat při přístupu k podrobnostem o zařízení.

![Zobrazit klíče nástroje BitLocker](./media/device-management-azure-portal/36.png)

Chcete-li zobrazit nebo zkopírovat klíče nástroje BitLocker, musíte být vlastníkem zařízení nebo uživatelem, kterému je přiřazena alespoň jedna z následujících rolí:

- Správce cloudových zařízení
- Globální správce
- Správce technické podpory
- Správce služby Intune
- Správce zabezpečení
- Čtečka zabezpečení

> [!NOTE]
> Hybridní zařízení Azure AD připojená k Windows 10 nemají vlastníka. Pokud tedy hledáte zařízení podle vlastníka a nenašli ho, vyhledejte ho podle ID zařízení.

## <a name="audit-logs"></a>Protokoly auditu

Aktivity zařízení jsou k dispozici prostřednictvím protokolů aktivit. Tyto protokoly zahrnují aktivity aktivované službou registrace zařízení a uživateli:

- Vytváření zařízení a přidávání vlastníků / uživatelů na zařízení
- Změny nastavení zařízení
- Operace zařízení, jako je odstranění nebo aktualizace zařízení

Vstupním bodem k datům auditování jsou **protokoly auditování** v části **Aktivita** na stránce **Zařízení.**

Protokol auditu má výchozí zobrazení seznamu, které zobrazuje:

- Datum a čas výskytu
- Cíle
- Iniciátor / herec (který) činnosti
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
- Iniciováno (herec)

Kromě filtrů můžete vyhledávat konkrétní položky.

![Protokoly auditu](./media/device-management-azure-portal/65.png)

## <a name="next-steps"></a>Další kroky

[Jak spravovat zastaralá zařízení ve službě Azure AD](manage-stale-devices.md)
