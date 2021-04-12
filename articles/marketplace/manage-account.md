---
title: Jak spravovat účet komerčního obchodu na webu Microsoft Partner Center – Azure Marketplace
description: Naučte se spravovat účet komerčního obchodu Marketplace v partnerském centru Microsoftu.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: varsha-sarah
ms.author: vavargh
ms.date: 04/07/2021
ms.openlocfilehash: c76d9d06425405cf7f43e089cb9c2995e30410ee
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/08/2021
ms.locfileid: "107108233"
---
# <a name="manage-your-commercial-marketplace-account-in-partner-center"></a>Správa účtu komerčního tržiště v partnerském centru

**Příslušné role**

- Vlastník
- Manažer

Po [Vytvoření účtu partnerského centra](./create-account.md)můžete pomocí [řídicího panelu komerčního tržiště](https://partner.microsoft.com/dashboard/commercial-marketplace/overview) spravovat svůj účet a nabídky.

## <a name="access-your-account-settings"></a>Přístup k nastavení účtu

Pokud jste to ještě neudělali, měli byste vy (nebo správce vaší organizace) získat přístup k [Nastavení účtu](https://partner.microsoft.com/dashboard/account/management) pro účet partnerského centra.

1. Přihlaste se na [řídicí panel komerčního tržiště](https://partner.microsoft.com/dashboard/commercial-marketplace) v partnerském centru s účtem, ke kterému chcete získat přístup. Pokud jste součástí několika účtů a přihlásili se k jinému účtu, můžete [Přepnout účty](switch-accounts.md).
1. V pravém horním rohu vyberte **Nastavení** (ikona ozubeného kolečka) a pak vyberte **Nastavení účtu**.

    [![Snímek nabídky nastavení účtu v partnerském centru ](./media/manage-accounts/settings-account.png)](./media/manage-accounts/settings-account.png#lightbox)

1. V části **Nastavení účtu** vyberte **právní**. Pak vyberte kartu **vývojář** a zobrazte podrobnosti související s vaším obchodním účtem na webu Marketplace.

    [![Snímek obrazovky karty Vývojář na stránce právního účtu v nastavení účtu. ](./media/manage-accounts/developer-tab.png)](./media/manage-accounts/developer-tab.png#lightbox)

### <a name="account-settings-page"></a>Stránka nastavení účtu

Když vyberete **Nastavení** a rozbalíte **Nastavení účtu**, výchozí zobrazení je **právní informace**. Tato stránka může obsahovat až tři karty v závislosti na programech, které jste zaregistrovali v: _partner_, _prodejce_ a _vývojář_.

Karta **partner** pro partnery zaregistrované v programu MPN zahrnuje:

- Všechny právní informace, jako jsou například registrované právní jméno a adresa pro vaši společnost
- Primární kontakt
- Firemní umístění.

Karta **prodejce** pro partnery, kteří provádějí CSP společnosti, zahrnuje:

- Primární kontaktní informace
- Zákaznická podpora – profil
- Informace o programu

Karta **vývojář** pro partnery zaregistrované v jakémkoli vývojářském programu obsahuje následující informace:

- **Podrobnosti účtu**: typ účtu a stav účtu
- **ID vydavatele**: ID prodejce, ID uživatele, ID vydavatele, klienti Azure AD a další
- **Kontaktní údaje**: zobrazované jméno vydavatele, kontakt prodejce (jméno, e-mail, telefon a adresa) a schvalovatel společnosti (jméno, e-mail, telefon)

### <a name="account-settings---developer-tab"></a>Nastavení účtu – karta Vývojář

Následující informace popisují informace na kartě Vývojář.

#### <a name="account-details"></a>Podrobnosti účtu

V části _Podrobnosti o účtu_ na kartě _vývojář_ uvidíte základní informace, například **typ účtu** (společnost nebo jednotlivec) a **stav ověření** vašeho účtu. Během procesu ověřování účtu se tato nastavení zobrazí každý požadovaný krok, včetně ověření e-mailu, ověřování zaměstnanosti a obchodního ověření.

#### <a name="publisher-ids"></a>ID vydavatele

V části ID vydavatelů uvidíte vaše **ID Symantec** (Pokud je k dispozici), **ID prodejce**, **ID uživatele**, **ID MPN** a **klienty Azure AD**. Tyto hodnoty přiřazuje Microsoft, aby jednoznačně identifikovaly váš vývojářský účet a nedají se upravovat.

Pokud nemáte ID Symantec, můžete vybrat odkaz pro vyžádání.

### <a name="contact-info"></a>Kontaktní údaje

V části _kontaktní údaje_ uvidíte **zobrazované jméno vydavatele**, **kontaktní údaje prodejce** (jméno kontaktu, e-mail, telefonní číslo a adresu prodejce společnosti) a **schvalovatel společnosti** (jméno, e-mail a telefonní číslo osoby s oprávněním ke schválení rozhodnutí společnosti).

Můžete také vybrat odkaz **aktualizace** a změnit kontaktní informace, jako je zobrazované jméno a e-mailová adresa vydavatele.

### <a name="account-settings-identifiers"></a>Identifikátory nastavení účtu

V části **Nastavení účtu**  >  **Profil organizace** vyberte **identifikátory** , abyste viděli následující informace:

- **ID MPN**: všechna ID MPN přidružená k vašemu účtu
- **CSP**: ID MPN přidružená k programu CSP pro tento účet.
- **Vydavatel**: ID prodejců přidružených k vašemu účtu
- **Sledovací identifikátory GUID**: všechny sledovací identifikátory GUID přidružené k vašemu účtu

#### <a name="tracking-guids"></a>Identifikátory GUID sledování

Globálně jedinečné identifikátory (GUID) jsou jedinečná referenční čísla (s 32 hexadecimálními číslicemi), která se dají použít ke sledování využití Azure.

Chcete-li vytvořit identifikátory GUID pro sledování, měli byste použít generátor GUID. Tým Azure Storage vytvořil [formulář generátoru GUID](https://aka.ms/StoragePartners) , který pošle e-mailem identifikátor GUID správného formátu a bude možné ho znovu použít v různých sledovacích systémech.

Pro každou nabídku a distribuční kanál pro každý produkt doporučujeme vytvořit jedinečný identifikátor GUID. Pokud nechcete, aby vytváření sestav bylo rozděleno, můžete použít jeden identifikátor GUID pro více distribučních kanálů produktu.

Pokud nasadíte produkt pomocí šablony a je k dispozici na Azure Marketplace i na GitHubu, můžete vytvořit a zaregistrovat dva jedinečné identifikátory GUID:

- Produkt A v Azure Marketplace
- Produkt A na GitHubu

Vytváření sestav se provádí na základě hodnoty partnera (ID partnera Microsoftu) a identifikátorů GUID. Můžete také sledovat identifikátory GUID na podrobnější úrovni, které odpovídají každému plánu v rámci vaší nabídky.

Další informace najdete v tématu [sledování zákaznického využití Azure pomocí nejčastějších dotazů k identifikátorům GUID](azure-partner-customer-usage-attribution.md#faq)).

### <a name="agreements"></a>Smlouvy

Stránka **smlouvy** vám umožňuje zobrazit seznam smluv pro publikování, které jste povolili. Tyto smlouvy jsou uvedené v souladu s názvem a číslem verze včetně data přijetí a jména uživatele, který smlouvu přijal.

Přístup ke stránce smlouvy:

1. Přihlaste se k [partnerskému centru](https://partner.microsoft.com/dashboard/home).
1. V pravém horním rohu vyberte **Nastavení**  >  **účtu** nastavení.
1. V části **Nastavení účtu** vyberte **smlouvy**.

Pokud jsou k dispozici aktualizace smluv, které vyžadují vaši pozornost, můžou se v horní části této stránky zobrazit **potřebné akce** . Pokud chcete přijmout aktualizovanou smlouvu, nejdřív si přečtěte verzi odkazované smlouvy a pak vyberte **přijmout smlouvu**.

## <a name="set-up-a-payout-profile"></a>Nastavení profilu výběr

Profil výběr je bankovní účet, na který se z prodeje posílají výnosy. Tento bankovní účet musí být ve stejné zemi nebo oblasti, kde jste zaregistrovali účet partnerského centra. Další informace o profilu typu výběr najdete v tématech [Vytvoření a Správa pobídek výběr a daňové profily v partnerském centru](/partner-center/incentives-create-and-manage-your-payout-and-tax-profiles) a [Nastavení účtu výběr a daňového formuláře](/partner-center/set-up-your-payout-account).

Postup nastavení profilu výběr:

1. Přejít na [stránku Přehled komerčního tržiště](https://partner.microsoft.com/dashboard/commercial-marketplace/overview) v partnerském centru.
2. V části **profil** klikněte vedle pole **profil** výběru na možnost **aktualizovat**.
3. **Vyberte způsob platby**: bankovní účet nebo PayPal.
4. **Přidat platební informace**: může to zahrnovat výběr typu účtu (kontroly a úspory), zadání názvu držitele účtu, čísla účtu a čísla směrování, fakturační adresy, telefonního čísla nebo e-mailové adresy PayPal. Další informace o používání služby PayPal jako způsobu platby na účet a zjištění, jestli se podporuje na vašem trhu nebo v oblasti, najdete v tématu [informace o PayPal](/windows/uwp/publish/setting-up-your-payout-account-and-tax-forms#paypal-info).

> [!IMPORTANT]
> Změna účtu výběr může zpozdit vaše platby až do jednoho platebního cyklu. K tomuto zpoždění dochází, protože potřebujeme ověřit změnu účtu, stejně jako při prvním nastavování účtu výběr. Po ověření účtu se pořád bude platit celá částka. jakékoli platby splatné za aktuální platební cyklus budou přidány do dalšího.  

## <a name="tax-profile"></a>Daňový profil

Zkontrolujte aktuální stav daňového profilu a zobrazí se správné informace o **typu entity** a **daňovém certifikátu** . Vyberte **Upravit** , pokud chcete aktualizovat nebo dokončit všechny požadované formuláře.

Aby bylo možné stanovit svůj daňový stav, musíte určit svou zemi nebo oblast pobytu a občanství a dokončit příslušné daňové formuláře spojené s vaší zemí nebo oblastí.

Bez ohledu na zemi nebo oblast pobytu nebo občanství musíte vyplnit USA daňové formuláře a prodávat jakékoli nabídky prostřednictvím Microsoftu. Partneři, kteří splňují určité USA požadavky na zaplňování, musí vyplnit formulář finančního úřadu W-9. Jiní partneři mimo USA musí vyplnit formulář finančního úřadu W-8. Tyto formuláře můžete po dokončení finančního profilu vyplnit online.

USA jednotlivé daňové identifikační číslo (nebo ITIN) se nevyžaduje pro příjem plateb od společnosti Microsoft nebo nárokování na výhody daňové smlouvy.

Daňové formuláře můžete v partnerském centru doplňovat a odesílat elektronicky. ve většině případů nemusíte tisknout ani tisknout žádné formuláře.

Různé země a oblasti mají různé daňové požadavky. Přesná částka, kterou musíte zaplatit za daně, závisí na zemích a oblastech, kde prodáváte své nabídky. Společnost Microsoft se zakládá za prodej a v některých zemích a oblastech používá daň za vás. Tyto země a oblasti budou identifikovány v procesu výpisu vaší nabídky. V ostatních zemích a oblastech, podle toho, kam jste zaregistrovali, může být nutné uhradit prodej a daň z prodeje přímo k místní autoritě zdanění. Tržby, které obdržíte, se navíc můžou zdanitelně vyvažovat za příjem. Důrazně doporučujeme, abyste se obrátili na příslušné oprávnění pro vaši zemi nebo oblast, která vám nejlépe pomůže určit správné daňové informace pro prodejní transakce od společnosti Microsoft.

Další informace o daňovém profilu najdete v tématu [Vytvoření a Správa pobídek výběrů a daňových profilů v partnerském centru](/partner-center/incentives-create-and-manage-your-payout-and-tax-profiles) a [Nastavení účtu typu výběr a daňového formuláře](/partner-center/set-up-your-payout-account).

### <a name="withholding-rates"></a>Míry odmítnutí

Informace, které odesíláte v daňovém formuláři, určují odpovídající míru srážkové srážky. Míra odmítnutí se vztahuje pouze na prodej, který provedete do USA; prodej uskutečněný v jiných umístěních než USA nezávisí na odmítnutí. Sazby pro srážka se liší, ale u většiny vývojářů, kteří registrují mimo USA, je výchozí sazba 30%. Tuto sazbu máte možnost snížit, pokud se země nebo oblast dohodla se smlouvou o dani ze zisku s USA.

### <a name="tax-treaty-benefits"></a>Výhody daňové smlouvy

Pokud nejste mimo USA, možná budete moct využít výhody daňové smlouvy. Tyto výhody se liší od zemí/oblastí až po zemi nebo oblast a můžou vám snížit množství daní, které společnost Microsoft odpírá. Výhody daňové smlouvy můžete uplatnit vyplněním části II formuláře W-8BEN. Doporučujeme, abyste komunikovali s příslušnými prostředky ve vaší zemi nebo oblasti, abyste zjistili, jestli se vám tyto výhody vztahují.

[Přečtěte si další informace o daňových detailech pro vývojáře aplikací a her pro Windows a Azure Marketplace vydavatelům](/windows/uwp/publish/tax-details-for-paid-apps).

### <a name="payout-hold-status"></a>Stav blokování ve výběr

Společnost Microsoft standardně odesílá platby měsíčně. Můžete ale volitelně zadat své výběry, které zabrání odeslání plateb vašemu účtu. Pokud se rozhodnete, že se vaše výběry zablokují, budeme nahrávat všechny výnosy, které získáte, a poskytnout podrobné údaje v **souhrnu** výběru. Do vašeho účtu ale nepošleme žádné platby, dokud ho neodeberete.

**Postup při zablokování plateb**:

1. Přejít na **Nastavení účtu**. 
1. V levém navigačním okně rozbalte **Výběr a daň** a vyberte výběr **a daňové profily**.
1. Vyberte program, pro který chcete uchovat platby, a pak zaškrtněte políčko **podržet moji platbu** .

Stav blokování můžete kdykoli změnit, ale mějte na paměti, že vaše rozhodnutí bude mít vliv na další měsíční výběr. Pokud například chcete pozastavit výběr z dubna, ujistěte se, že jste na konci března nastavili stav blokování vstupu na **zapnuto** .

Po nastavení stavu blokování výběr na **zapnuto** budou všechny výběry podrženy, dokud nepřepnete posuvník zpět na **vypnuto**. Když to uděláte, budete zahrnuti během měsíčního cyklu výběrů (za předpokladu, že byly splněny příslušné prahové hodnoty plateb). Například pokud máte vaše výběry podrženy, ale chcete mít vygenerovaný výběr v červnu, nezapomeňte přepnout stav typu blokování na **vypnuto** před koncem květen.

> [!NOTE]
> Výběr **stavu blokování** výběru se vztahuje na **všechny** zdroje příjmů placené prostřednictvím partnerského centra Microsoftu, včetně Azure Marketplace, Microsoft AppSource, Microsoft Store, reklamy atd.). Pro každý zdroj výnosů nemůžete vybrat jiné stavy blokování.

## <a name="devices"></a>Zařízení

Nastavení správy zařízení se týká jenom publikování na platformě pro univerzální platformu Windows (UWP). [Další informace](/windows/uwp/publish/manage-account-settings-and-profile#additional-settings-and-info).

## <a name="create-a-billing-profile"></a>Vytvořit fakturační profil

Pokud publikujete [dynamics 365 pro zákaznickou zapojení & Power Apps](./partner-center-portal/create-new-customer-engagement-offer.md) nebo [Dynamics 365 for Operations](./partner-center-portal/create-new-operations-offer.md) nabídka, musíte dokončit svůj *Fakturační profil*.

Fakturační adresa je předem vyplněná z vaší právnické osoby a tuto adresu můžete později aktualizovat. Pole DPH a ID DPH jsou vyžadována pro některé země a volitelné pro ostatní. Název země nebo oblasti a název společnosti nelze upravovat.

1. Přejít na **Nastavení účtu**.
1. Pak v levém navigačním okně rozbalte možnost **Profil organizace** a vyberte možnost **profil fakturace**.


## <a name="multi-user-account-management"></a>Správa účtů s více uživateli

Partnerské centrum používá [Azure Active Directory](/azure/active-directory/fundamentals/active-directory-whatis) (Azure AD) pro přístup a správu účtu s více uživateli. Služba Azure AD vaší organizace se v rámci procesu registrace automaticky přidruží k vašemu účtu partnerského centra.

## <a name="next-steps"></a>Další kroky

- [Přidávání a správa uživatelů](add-manage-users.md)
