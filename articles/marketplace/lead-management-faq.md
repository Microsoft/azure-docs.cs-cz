---
title: Otázky správy potenciálních zákazníků a řešení potíží – partnerské Centrum Microsoftu
description: Přečtěte si o běžných chybách a dotazech při konfiguraci zájemců komerčního tržiště v partnerském centru
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: keferna
ms.author: keferna
ms.date: 10/01/2020
ms.openlocfilehash: 681498073a5f111e3babd50443623589ffeec02f
ms.sourcegitcommit: 03713bf705301e7f567010714beb236e7c8cee6f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/21/2020
ms.locfileid: "92330916"
---
# <a name="common-questions-and-troubleshooting-for-lead-configuration"></a>Běžné dotazy a řešení potíží s konfigurací potenciálních zákazníků

Tento článek obsahuje odpovědi na některé běžné dotazy týkající se správy zájemců pro vaše nabídky komerčního tržiště. Také řeší chyby, se kterými se můžete setkat při konfiguraci zájemců do systému pro správu vztahů se zákazníky (CRM) v partnerském centru.

## <a name="common-questions-about-lead-management"></a>Běžné otázky týkající se správy potenciálních zákazníků

#### <a name="where-can-i-get-help-in-setting-up-my-lead-destination"></a>Kde můžu získat pomoc při nastavování cíle realizace?

Přehled o tom, jak připojit váš systém CRM k vašim komerčním nabídkám, najdete v tématu o [zákaznících z komerční nabídky na webu Marketplace](partner-center-portal/commercial-marketplace-get-customer-leads.md) . Pokud máte chybu, přečtěte si pokyny k řešení potíží níže. Pokud chcete získat další podporu, odešlete lístek podpory prostřednictvím [centra pro nápovědu a podporu pro partnerský server](https://aka.ms/marketplacepublishersupport). Pak vyberte **Nabídka vytvořit**  >  **váš typ**pro  >  **správu správy potenciálních zákazníků**.

#### <a name="am-i-required-to-configure-a-lead-destination-in-order-to-publish-an-offer-in-the-commercial-marketplace"></a>Je nutné nakonfigurovat cíl zájemce, aby bylo možné publikovat nabídku na komerčním webu Marketplace?

Odpověď závisí na typu nabídky, kterou publikujete. Software jako služba (SaaS) a Dynamics 365 Customer Engagement použijte **kontaktní mě** k vypsání všech Dynamics 365 pro nabídky finance a Operations, všech Dynamics 365 Business Central nabízí a všech nabídek služeb. V důsledku toho vyžadují připojení k cíli zájemce. Pokud váš typ nabídky není uveden, připojení k cíli zájemce není vyžadováno. Doporučujeme, abyste nakonfigurovali cíl zájemce, abyste nemuseli přijít o obchodní příležitosti.

#### <a name="how-can-i-find-the-test-lead"></a>Jak můžu najít vedoucí test?

Vyhledejte `"MSFT_TEST"` v cíli zájemce. Níže je ukázkový vedoucí testu od Microsoftu. Všimněte si, že formát vedoucího testu se liší v závislosti na cíli zájemce.

```
{
    "userDetails": {
      "FirstName": "MSFT_TEST_636573304831318844",
      "LastName": "MSFT_TEST_636573304831318844",
      "Email": "MSFT_TEST_636573304831318844@test.com",
      "Phone": "1234567890",
      "Country": "US",
      "Company": "MSFT_TEST_636573304831318844",
      "Title": "MSFT_TEST_636573304831318844"
    },
    "LeadSource": "AzureMarketplace",
    "ActionCode": "INS",
    "OfferTitle": "Contoso Test"
    "Description": "MSFT_TEST_636573304831318844"
}
```

#### <a name="i-have-a-live-offer-but-why-am-i-not-seeing-any-leads"></a>Mám živou nabídku, ale proč mi nevidím nějaké potenciální zákazníky?

Ujistěte se, že je připojení k cíli realizace platné. Po výběru **publikovat** na vaší nabídce v partnerském centru vám pošleme vedoucího testu. Pokud se zobrazí vedoucí testu, připojení je platné. Můžete také otestovat své zájemce tím, že se v kroku Preview pokusíte získat náhled nabídky. Vyberte **získat hned**, **kontaktujte mě**nebo **bezplatnou zkušební verzi** na výpisu na komerčním webu Marketplace.

Ujistěte se také, že hledáte správná data. Přečtěte si informace o [potenciálních](partner-center-portal/commercial-marketplace-get-customer-leads.md) datech pro vysvětlení údajů o zájemcích, které odesíláme do vašeho cíle potenciálního zákazníka.

#### <a name="i-configured-azure-blob-storage-as-my-lead-destination-but-why-dont-i-see-the-lead"></a>Nakonfiguroval (a) jsem úložiště objektů BLOB v Azure jako cíl pro zájem, ale proč nevidím potenciálního zákazníka?

Azure Blob Storage už není podporovaný jako cíl pro realizace, takže nebudete mít žádné zájemce vygenerované vaší nabídkou. Přepněte na kteroukoli z dalších [možností cíle pro zájemce](partner-center-portal/commercial-marketplace-get-customer-leads.md). 

#### <a name="i-received-an-email-from-the-commercial-marketplace-but-why-cant-i-find-the-lead-in-my-crm"></a>Dostal jsem e-mail z komerčního tržiště, ale proč nemůžu v mém CRM najít potenciálního zákazníka?

Je možné, že e-mailová doména koncového uživatele pochází z. edu. Z důvodů ochrany osobních údajů nemusíme předávat osobní údaje z domény. edu. Odešlete lístek podpory prostřednictvím [odborné pomoci a podpory pro partnerským centru](https://aka.ms/marketplacepublishersupport).

#### <a name="i-configured-an-azure-table-as-my-lead-destination-how-can-i-view-the-leads"></a>Nakonfigurovali jsem tabulku Azure jako cíl pro realizace. Jak si můžu zobrazit potenciální zákazníky?

K datům zájemce uloženým v tabulce Azure můžete přistupovat z Azure Portal. Můžete si také stáhnout a nainstalovat [Průzkumník služby Azure Storage](https://azure.microsoft.com/features/storage-explorer/) zdarma a zobrazit tak data z tabulky vašeho účtu úložiště Azure.

#### <a name="i-configured-an-azure-table-as-my-lead-destination-can-i-get-notified-whenever-a-new-commercial-marketplace-lead-is-sent"></a>Nakonfigurovali jsem tabulku Azure jako cíl pro realizace. Můžu dostávat oznámení při každém odeslání nového potenciálního zákazníka z obchodu Marketplace?

Ano. Postupujte podle pokynů v tématu [Konfigurace řízení zájemců pomocí tabulky Azure](partner-center-portal/commercial-marketplace-lead-management-instructions-azure-table.md) k nastavení služby Microsoft flow, která odešle e-mail, když se do tabulky Azure přidá zájemce.

#### <a name="i-configured-salesforce-as-my-lead-destination-but-why-cant-i-find-the-leads"></a>Nakonfigurovali jste Salesforce jako cíl pro zájem, ale proč nemůžu najít potenciální zákazníky?

Zkontroluje, jestli je formulář Web-to-zájemce povinným polem na základě seznamu vyskladnění. Pokud je, přepněte pole na nepovinné textové pole.

#### <a name="there-was-an-issue-with-my-lead-destination-and-i-missed-some-leads-can-i-have-them-sent-to-me-in-an-email"></a>Došlo k potížím s cílem mého zájemce a vynechali nějaké zájemce. Můžu se mi poslat do e-mailu?

V důsledku zásad osobních informací nemůžeme sdílet informace o potenciálních osobách prostřednictvím nezabezpečeného e-mailu.

#### <a name="i-configured-an-azure-table-as-my-lead-destination-how-much-will-it-cost"></a>Nakonfigurovali jsem tabulku Azure jako cíl pro realizace. Kolik to bude stát?

Data generování zájemců jsou nízká. Téměř všem vydavatelům je méně než 1 GB. Náklady závisí na počtu přijatých zájemců. Například pokud 1 000 potenciální zákazníci obdrží za měsíc, náklady jsou přibližně 50 centů. Další informace o cenách služby Storage najdete v tématu [Azure Storage přehledu cen](https://azure.microsoft.com/pricing/details/storage/).

Pokud na vaši otázku neodpovíte, kontaktujte podpora Microsoftu prostřednictvím [centra pro partnery a pomoc a podporu](https://aka.ms/marketplacepublishersupport). Pak vyberte **Nabídka vytvořit**  >  **váš typ**pro  >  **správu správy potenciálních zákazníků**.

#### <a name="im-receiving-email-notifications-when-new-customer-leads-are-received-how-can-i-configure-someone-else-to-receive-these-emails"></a>Přijímám e-mailová oznámení, když se přijímají Noví zájemci zákazníků. Jak můžu nakonfigurovat někoho jiného, aby přijímal tyto e-maily?

Přihlaste se k nabídce v partnerském centru a přejděte na stránku **nastavení nabídky** > **Správa zájemců**  >  **Upravit**. Aktualizujte e-mailové adresy v poli **kontaktní e-mail** .

## <a name="troubleshooting-lead-configuration-errors"></a><a id="publishing-config-errors"></a> Řešení chyb konfigurace potenciálních zákazníků

**Nepovedlo se uložit zájemce do Dynamics CRM. Ověřte nastavení účtu Dynamics CRM. LastCRMError: nepovedlo se přihlásit k Dynamics CRM, LastCRMException:** 

> Pokud byla vybrána možnost ověřování Microsoft 365, ověřte, zda je uživatelský účet a heslo platné. Pokud jste vybrali Azure Active Directory, podívejte se, jestli ID tenanta, ID aplikace a tajný klíč aplikace odpovídají obsahu, který jste nastavili v Azure Active Directory. Postupujte podle [pokynů.](./partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics.md) Pokud je uživatelské jméno/heslo účtu platné, ujistěte se prosím, že má přístup k Dynamics 365 a má přiřazenou licenci (kroky 11-15 při použití Azure Active Directory nebo nastavení zabezpečení při použití uživatele Office). 

**Nepovedlo se uložit zájemce do Dynamics CRM. Uživatel nemá oprávnění k vytvoření pro atribut leadsourcecode v entitě potenciálního zákazníka.** 

> V aplikaci nebo uživateli chybí role zabezpečení pro Microsoft Marketplace zapisovače potenciálních zákazníků. Pokud [používáte uživatele Office, postupujte](./partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics.md)podle kroků 11-15, pokud používáte Azure Active Directory, nebo nastavení zabezpečení.

**Nepovedlo se uložit zájemce do Dynamics CRM pomocí AAD. Výjimka:: tenant se nenašel. Tato instance může nastat, pokud pro tenanta nejsou k dispozici žádná aktivní předplatná.**  

> ID adresáře, které je uvedené v části Správa zájemců, není platný adresář. Získejte prosím ID adresáře podle pokynů v kroku 2 (v části [Azure Active Directory).](./partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics.md)

**Nepovedlo se uložit zájemce do Dynamics CRM. LastCRMError: SecLib:: RetrievePrivilegeForUser se nezdařilo – uživateli nejsou přiřazeni žádné role.**  

> Řešení: Přiřaďte roli zabezpečení pro Microsoft Marketplace zapisovače potenciálních zákazníků. Postupujte podle [pokynů v části](./partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics.md) nastavení zabezpečení.

**Nepovedlo se uložit zájemce do Dynamics CRM pomocí AAD. Výjimka:: aplikace s identifikátorem nebyla v adresáři nalezena.** 

> ID aplikace uvedené v části Správa zájemců není platný adresář. Získejte prosím ID adresáře podle pokynů v kroku 8 (v části Azure Active Directory, [odsud).](./partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics.md) 

**Nepovedlo se uložit zájemce do Dynamics CRM pomocí AAD. Výjimka:: požadovaný identifikátor tenanta není platný a není platný formát externí domény.** 

> ID adresáře, které je uvedené v části Správa zájemců, není platný adresář. Získejte prosím ID adresáře podle pokynů v kroku 2 (v části Azure Active Directory, [odsud).](./partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics.md) 

**Nepovedlo se uložit zájemce do Dynamics CRM pomocí AAD. Výjimka:: Chyba při ověřování přihlašovacích údajů. byl zadán neplatný tajný klíč klienta.** 

> Řešení: Přihlaste se do Azure Portal a ověřte, jestli se klíč aplikace shoduje s obsahem partnerského centra. Vygenerujte prosím heslo na základě instrukcí v kroku 10 (v části Azure Active Directory), z [tohoto místa](./partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics.md)). 

**Nepovedlo se uložit zájemce do Dynamics CRM. LastCRMError: při čekání na odpověď po 00:02:00 vypršel časový limit kanálu požadavků. Zvyšte hodnotu časového limitu předaného pro volání a vyžádejte si nebo zvyšte hodnotu SendTimeout na vazbě. Čas přidělený této operaci byl pravděpodobně částí delšího časového limitu.**  

> Řešení: Přihlaste se k partnerskému centru, vyhledejte nastavení nabídky >> potenciální zákazníci >> adrese URL a ověřte, jestli se jedná o platnou instanci Dynamic CRM.

