---
title: Publikovat nabídku spravovaných služeb na webu Azure Marketplace
description: Informace o publikování nabídky na spravovanou službu, že zákazníci připojí k Azure delegovat správu prostředků.
author: JnHs
ms.author: jenhayes
ms.service: lighthouse
ms.date: 07/11/2019
ms.topic: overview
manager: carmonm
ms.openlocfilehash: bb2f26a170bbd60eb927bd00f6def7d033fafee9
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2019
ms.locfileid: "67809845"
---
# <a name="publish-a-managed-services-offer-to-azure-marketplace"></a>Publikovat nabídku spravovaných služeb na webu Azure Marketplace

V tomto článku se dozvíte víc o publikování nabídky veřejného nebo privátního spravované služby pro [Azure Marketplace](https://azuremarketplace.microsoft.com) pomocí [portál partnerů cloudu](https://cloudpartner.azure.com/), povolení zákazníky, kteří zakoupí nabídky, který se má připojit Azure delegovat správu prostředků. 

> [!NOTE]
> Musíte mít platný [účet v partnerském centru](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-account) vytvoření a publikování tyto nabídky. Pokud nemáte účet již, [procesu registrace](https://aka.ms/joinmarketplace) povede vás provede postupem vytvoření účtu v partnerském centru a registrace v programu komerční Marketplace. Vaše ID Microsoft Partner Network (MPN) bude [automaticky přiřazen](https://docs.microsoft.com/azure/billing/billing-partner-admin-link-started) pomocí nabídky publikujete sledovat vliv mezi zákazníky.
>
> Pokud nechcete, aby s publikováním nabídky na webu Azure Marketplace, je možné onboardingu zákazníků ručně pomocí šablon Azure Resource Manageru. Další informace najdete v tématu [zapojení zákazníků Azure Delegovaná správa prostředků](onboard-customer.md).

Publikování služby spravované je podobná publikování jakéhokoli jiného typu nabídky na webu Azure Marketplace nabídku. Další informace o tomto procesu najdete v tématu [Azure Marketplace a AppSource Průvodce publikováním](https://docs.microsoft.com/azure/marketplace/marketplace-publishers-guide) a [nabídky Spravovat Azure a AppSource Marketplace](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/manage-offers/cpp-manage-offers).

> [!IMPORTANT]
> Zahrnují jednotlivé plány v nabídku spravovaných služeb **Manifest podrobnosti** části, kde definujete entity služby Azure Active Directory (Azure AD) ve vašem tenantovi, který bude mít přístup k delegovanému prostředku skupiny nebo předplatného pro Zákazníci, kteří si koupí plán. Je důležité vědět, že žádné skupině (nebo uživatel nebo instanční objekt služby), kterou tady bude mít stejná oprávnění pro každý zákazník, kteří zakoupí plánu. Přiřadit různé skupiny pro práci s každého zákazníka, musíte publikovat samostatné privátní plán, který je určen výhradně pro každého zákazníka.

## <a name="create-your-offer-in-the-cloud-partner-portal"></a>Vytvoření vaší nabídky v portál Cloud Partner

1. Přihlaste se k [portál partnerů cloudu](https://cloudpartner.azure.com/).
2. V levé navigační nabídce vyberte **nová nabídka**a pak vyberte **spravovaných služeb**.
3. Zobrazí se vám **Editor** části zadejte vaši nabídku s čtyři části: **Nastavení nabídky**, **plány**, **Marketplace**, a **podporu**. Přečtěte si informace o tom, jak dokončit tyto části.

## <a name="enter-offer-settings"></a>Zadejte nastavení nabídky

V **nabízejí nastavení** části, zadejte následující:

|Pole  |Popis  |
|---------|---------|
|**ID nabídky**     | Jedinečný identifikátor pro vaši nabídku (v rámci svůj profil vydavatele). Toto ID může obsahovat jenom malé alfanumerické znaky, pomlčky a podtržítka, nesmí být delší než 50 znaků. Uvědomte si, že může být ID nabídky pro zákazníky v místech, jako jsou v produktu adresy URL a účetních sestav viditelné. Po publikování nabídky tuto hodnotu nemůžete změnit.        |
|**ID vydavatele**     | ID vydavatele, která bude spojená s nabídkou. Pokud máte více než jeden ID vydavatele, můžete vybrat ta, kterou chcete použít pro tuto nabídku.       |
|**Název**     | Název (až 50 znaků), který zákazníci uvidí pro nabídky na webu Azure Marketplace a na webu Azure Portal. Použít rozpoznat název značky, které zákazníci rozumět, pokud v rámci této nabídky máte podporu prostřednictvím svůj vlastní web, nezapomeňte použít zde přesně stejný název.        |

Když jste hotovi, vyberte **Uložit**. Nyní jste připraveni přejít na **plány** oddílu.

## <a name="create-plans"></a>Vytváření plánů

Každou nabídku musí mít jeden nebo více plánů (někdy označované jako skladové položky). Můžete například přidat více plánů pro podporu různých funkčních sad na různé ceny nebo přizpůsobit konkrétní plán pro určité cílové skupině konkrétní zákazníků. Zákazníci můžou podívat plány, které jsou pro ně k dispozici v nadřazené nabídky.

V části plány pro každý plán, kterou chcete vytvořit, vyberte **nový plán**. Zadejte **ID plánu**. Toto ID může obsahovat jenom malé alfanumerické znaky, pomlčky a podtržítka, nesmí být delší než 50 znaků. ID plánu může být viditelné pro zákazníky v místech, jako jsou v produktu adresy URL a účetních sestav. Po publikování nabídky tuto hodnotu nemůžete změnit.

Dále provedením v následujících částech **podrobnosti plánu** části:

|Pole  |Popis  |
|---------|---------|
|**Název**     | Popisný název plánu pro zobrazení. Maximální délka 50 znaků.        |
|**Shrnutí**     | Stručný popis plánu pro zobrazení pod nadpisem. Delší než 100 znaků.        |
|**Popis**     | Text popisu, který poskytuje podrobnější vysvětlení plánu.         |
|**Model fakturace**     | Existují 2 modely fakturace, které jsou tady uvedené, ale je nutné vybrat **používání vlastní licence** nabídky spravovaných služeb. To znamená, že bude fakturovat svým zákazníkům přímo náklady související se v rámci této nabídky a Microsoft neúčtují žádné poplatky se vám.   |
|**Je to privátní plán?**     | Určuje, zda SKU je privátní nebo veřejné. Výchozí hodnota je **ne** (veřejné). Ponecháte-li tento výběr, váš plán nebude omezen konkrétní zákazníkům (nebo počtu zákazníků); Po publikování veřejný plán, nemůžete později změnit ho na soukromou. Chcete-li tento plán k dispozici jenom pro konkrétní zákazníky, vyberte **Ano**. Pokud tak učiníte, budete potřebovat k identifikaci zákazníků tím, že poskytuje ID předplatného. Mohou to být zadané jeden po druhém (pro až 10 předplatná) nebo tak, že nahrajete soubor .csv (pro až 20 000 předplatná). Ujistěte se, že obsahují vlastní odběry tady, takže můžete otestovat a ověřit nabídky. Další informace najdete v tématu [privátní SKU a plány](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-azure-private-skus).  |

Nakonec proveďte **Manifest podrobnosti** oddílu. Tím se vytvoří manifest s informacemi o autorizaci pro správu prostředků služby zákazníka. Informace, které zadáte tady je potřeba připojit svým zákazníkům Azure delegovat správu prostředků. Jak bylo uvedeno výše, tato oprávnění má platit pro každý zákazník, kteří zakoupí plánu, takže pokud chcete omezit přístup pro konkrétního zákazníka, budete muset publikovat privátní plán pro svoje výhradní použití.

- Nejdřív zadejte **verze** pro manifest. Použijte formát *n.n.n* (například 1.2.5).
- Pak zadejte vaše **ID Tenanta**. Toto je identifikátor GUID přidružený k ID tenanta Azure Active Directory vaší organizace (například tenanta, který budete pracovat v ke správě prostředků vašich zákazníků). Pokud nemáte tomto po ruce, najdete ho ukazatele myši na název svého účtu na pravé straně horním rohu webu Azure portal, nebo výběrem **přepnout adresář**. 
- Nakonec přidejte jeden nebo více **autorizace** položky do plánu. Povolení definují entity, kteří mají přístup k prostředkům a předplatná pro zákazníky, kteří si koupí plán. Je nutné zadat tyto informace za účelem přístupu k prostředkům jménem zákazníka pomocí prostředků Azure delegovanou správu.
  Pro jednotlivé autorizace zadejte následující. Pak můžete vybrat **nové autorizační** tolikrát, kolikrát podle potřeby přidat další definice uživatele/role.
  - **ID objektu Azure AD**: Azure AD identifikátor uživatele, skupiny uživatelů nebo aplikací, které bude udělen určitá oprávnění (jak je popsáno v definici Role) k prostředkům vašich zákazníků.
  - **Zobrazovaný název objektu služby Azure AD**: Popisný název, který pomůže zákazníky, pochopit účel Tato autorizace. Zákazník uvidí tento název po přidělení prostředků.
  - **Definice role**: Vyberte jednu z dostupných předdefinované role Azure AD ze seznamu. Tato role určuje oprávnění, která uživatele **ID objektu Azure AD** pole bude mít na vaše zákazníky prostředky. Informace o těchto rolích najdete v tématu [předdefinované role](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles).
  - **Přiřadit role**: Pokud jste vybrali správce přístupu uživatelů **definice Role** Tato autorizace, můžete přidat jeden nebo více přiřadit role tady. Uživatel v **ID objektu Azure AD** pole bude moct přiřadit tyto **přiřadit role** k [spravovaných identit](https://docs.microsoft.com/azure/managed-applications/publish-managed-identity). Všimněte si, že žádná další oprávnění, které jsou obvykle spojené s rolí správce uživatelských přístupů uplatní na tohoto uživatele. (Pokud jste nevybrali správce uživatelských přístupů pro definici Role tohoto uživatele, toto pole nemá žádný vliv.)

> [!TIP]
> Ve většině případů budete chtít přiřadit oprávnění na skupině uživatelů Azure AD nebo služba instančního objektu, nikoli na řadu jednotlivých uživatelských účtů. To umožňuje přidat nebo odebrat přístup pro jednotlivé uživatele bez nutnosti aktualizovat a znovu publikovat plánu, když se změní vaše požadavky na přístup.

Po dokončení přidávání plánů, vyberte **Uložit**, pokračujte **Marketplace** oddílu.

## <a name="provide-marketplace-text-and-images"></a>Zadejte Marketplace textu a obrázků

**Marketplace** je oddíl, ve kterém můžete zadat text a obrázky, které zákazníkům se zobrazí v Azure Marketplace a na webu Azure portal.

Zadejte informace pro následující pole v **přehled** části:

|Pole  |Popis  |
|---------|---------|
|**Název**     |  Název nabídky, často dlouhý, oficiální název. Tento název se zobrazí výrazném místě na webu Marketplace. Maximální délka 50 znaků. Ve většině případů to by měl být stejný jako **název** jste zadali v **nabízejí nastavení** oddílu.       |
|**Shrnutí**     | Stručný účel nebo funkce o vaší nabídce. Obvykle se zobrazí pod nadpisem. Delší než 100 znaků.        |
|**Dlouhé shrnutí**     | Delší souhrn účel nebo funkce o vaší nabídce. Delší než 256 znaků.        |
|**Popis**     | Další informace o vaší nabídce. Toto pole má maximální délku znaků 3000 a podporuje jednoduchý formátování HTML.        |
|**Identifikátor marketing**     | Jedinečný identifikátor URL skvěle. se používá v Marketplace adresy URL pro v rámci této nabídky. Například, pokud je vaše ID vydavatele *contoso* a marketingové identifikátor je *ukázková aplikace*, bude mít adresu URL pro nabídky na webu Azure Marketplace *https://azuremarketplace.microsoft.com/marketplace/apps/contoso.sampleApp* .        |
|**ID předplatných ve verzi Preview**     | Přidejte jeden až 100 identifikátorů předplatného. Zákazníci přidružené k těmto předplatným budou moci zobrazit nabídky na webu Azure Marketplace, před přejde za provozu. My Navrhujeme, včetně zde vlastní odběry, takže můžete zobrazit náhled jak zobrazuje vaší nabídky na webu Azure Marketplace před zpřístupněním zákazníků.  (Podpora Microsoftu a technické týmy budou moct zobrazit vaše nabídka během tohoto období preview.)   |
|**Užitečné odkazy**     | Adresy URL související s vaší nabídky, jako je například dokumentaci, poznámky k verzi, nejčastější dotazy, atd.        |
|**Navrhované kategorie (max. 5)**     | Jednu nebo více kategorií (až 5), které platí pro vaši nabídku. Tyto kategorie pomáhají zákazníkům zjistit vaší nabídky v Azure Marketplace a na webu Azure portal.        |

V **marketingové artefakty** oddílu, můžete nahrát, loga a dalších zdrojů, který má být zobrazen v rámci vaší nabídky. Volitelně můžete nahrát snímky obrazovky nebo odkazy na videa, která zákazníkům pomůže pochopit vaši nabídku.

Čtyři logo velikosti jsou požadovány: **Malý (40 x 40)** , **střední (90 x 90)** , **velké (115 x 115)** , a **celou (255 x 155)** . Postupujte podle následujících pokynů pro vaše loga:

- Design Azure má jednoduchou paletu barev. Omezte počet primárních a sekundárních barev ve vašem logu.
- Barvami motivů na portálu jsou bílá a černá. Nepoužívejte tyto barvy jako barvu pozadí pro vaše logo. Použijte barvu, která vaše logo na portálu zvýrazní. Doporučujeme použít jednoduché primární barvy.
- Pokud používáte průhledným pozadím, ujistěte se, že nejsou bílé, logem a text černá a modrá.
- Logo by mělo působit plochým dojmem a nemělo by obsahovat barevné přechody. Nepoužívejte v logu pozadí s barevným přechodem.
- Na logo neumisťujte žádný text, a to ani název vaší společnosti nebo název značky.
- Zajistěte, aby logo nebylo roztažené.

**Hero (815 x 290)** logo je volitelné, ale doporučené. Pokud zahrnete hero logo, postupujte podle následujících pokynů:

- Nechcete zahrnout jakýkoli text hero logo a nechte 415 pixelů volného místa na pravé straně loga. Je to nutné pro prostor textové prvky, které budou vloženy prostřednictvím kódu programu: zobrazované jméno vydavatele, název plánu nabídky, dlouhé shrnutí.
- Hero logo pozadí nemusí být černá, bílé nebo transparentní. Ujistěte se, že: barva pozadí není příliš nízké, protože vložený text se zobrazí v prázdné.
- Po publikování vaší nabídky s ikonou hero, nelze odebrat (i když jste ho mohli aktualizovat s jinou verzí v případě potřeby).

V **vést správu** oddílu, můžete vybrat systému CRM, kam se budou ukládat vaše potenciálních zákazníků, v případě potřeby. 

Nakonec zadejte vaše **adresa URL zásad ochrany osobních údajů** a **podmínky použití** v **právní** oddílu. Můžete také zadat, jestli se mají použít [standardní smlouvy](https://docs.microsoft.com/azure/marketplace/standard-contract) k této nabídce zaregistrují.

Nezapomeňte si uložit změny před přechodem na **podporu** oddílu.

## <a name="add-support-info"></a>Přidání informací o podpoře

V **podporují** části, zadejte jméno, e-mailu a telefonní číslo kontaktu podpory technickým kontaktem a zákazníka. Také budete muset poskytnout podporu adresy URL. Microsoft může použít tyto informace, až budeme potřebovat vás kontaktovat ohledně podnikání a zrychlit vyřešení problémů podpory.

Po přidání těchto informací, vyberte **uložit.**

## <a name="publish-your-offer"></a>Publikování nabídky

Jakmile budete spokojeni s všechny údaje, které jste zadali, je dalším krokem publikování nabídky na webu Azure Marketplace. Vyberte **publikovat** tlačítko zahájíte proces zpřístupnění vaší nabídky za provozu. Další informace o tomto procesu najdete v tématu [publikování Azure Marketplace a AppSource nabídky](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/manage-offers/cpp-publish-offer).

## <a name="next-steps"></a>Další postup

- Další informace o [napříč tenanty činnosti správy](../concepts/cross-tenant-management-experience.md).
- [Umožňuje zobrazit a spravovat zákazníky](view-manage-customers.md) tak, že přejdete do **zákazníci** na webu Azure Portal.
