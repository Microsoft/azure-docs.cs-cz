---
title: Publikování nabídky spravované služby na Azure Marketplace
description: Zjistěte, jak publikovat nabídku spravované služby, která zákazníkům zařazuje delegovanou správu prostředků Azure.
ms.date: 04/08/2020
ms.topic: conceptual
ms.openlocfilehash: 247f711188fa10de19cece27f164fdfa71612d1b
ms.sourcegitcommit: a53fe6e9e4a4c153e9ac1a93e9335f8cf762c604
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/09/2020
ms.locfileid: "80991905"
---
# <a name="publish-a-managed-service-offer-to-azure-marketplace"></a>Publikování nabídky spravované služby na Azure Marketplace

> [!IMPORTANT]
> dubna 2020 začneme přesouvat správu nabídek spravovaných služeb do Partnerského centra. Po migraci vytvoříte a spravujete nabídky v Centru partnerů. Postupujte podle pokynů v [části Vytvoření nové nabídky spravované služby](../../marketplace/partner-center-portal/create-new-managed-service-offer.md) pro správu migrovaných nabídek.

V tomto článku se dozvíte, jak publikovat veřejnou nebo soukromou nabídku spravované služby na [Azure Marketplace](https://azuremarketplace.microsoft.com) pomocí [portálu cloudových partnerů](https://cloudpartner.azure.com/). Zákazníci, kteří si zakoupí nabídku, pak mohou zavést předplatná a skupiny prostředků pro [správu delegovaných prostředků Azure](../concepts/azure-delegated-resource-management.md).

## <a name="publishing-requirements"></a>Požadavky na publikování

Chcete-li vytvářet a publikovat nabídky, musíte mít [v Partnerském centru](../../marketplace/partner-center-portal/create-account.md) platný účet. Pokud ještě nemáte účet, proces [registrace](https://aka.ms/joinmarketplace) vás provede kroky vytvoření účtu v Partnerském centru a registrace do programu Commercial Marketplace.

Podle požadavků na [certifikaci spravované služby](https://docs.microsoft.com/legal/marketplace/certification-policies#7004-business-requirements)musíte mít [úroveň kompetence silver nebo gold cloudové platformy](https://docs.microsoft.com/partner-center/learn-about-competencies) nebo být [msp experta Azure,](https://partner.microsoft.com/membership/azure-expert-msp) abyste mohli publikovat nabídku spravované služby.

ID sítě Microsoft Partner Network (MPN) bude [automaticky přidruženo](../../billing/billing-partner-admin-link-started.md) k nabídkám, které publikujete, aby bylo možné sledovat váš dopad na příčky zákazníků.

> [!NOTE]
> Pokud nechcete publikovat nabídku na Azure Marketplace, můžete zákazníky zaoklézat ručně pomocí šablon Azure Resource Manager. Další informace najdete [v tématu Onboard zákazníka azure delegované správy prostředků](onboard-customer.md).

Publikování nabídky spravované služby je podobné publikování jakéhokoli jiného typu nabídky na Azure Marketplace. Informace o obecném procesu publikování najdete v [tématu Azure Marketplace a Průvodce publikováním AppSource](../../marketplace/marketplace-publishers-guide.md). Měli byste si také přečíst [zásady certifikace komerčního tržiště](https://docs.microsoft.com/legal/marketplace/certification-policies), zejména část [Spravované služby.](https://docs.microsoft.com/legal/marketplace/certification-policies#700-managed-services)

Jakmile zákazník přidá vaši nabídku, bude moct delegovat jedno nebo více předplatných nebo skupin prostředků, které pak budou [na palubě pro správu delegovaných prostředků Azure](#the-customer-onboarding-process).

> [!IMPORTANT]
> Každý plán v nabídce spravované služby zahrnuje oddíl **Podrobnosti manifestu,** kde definujete entity Azure Active Directory (Azure AD) ve vašem tenantovi, které budou mít přístup k delegovaným skupinám prostředků nebo předplatným pro zákazníky, kteří si tento plán zakoupí. Je důležité si uvědomit, že každá skupina (nebo uživatel nebo instanční objekt), který zahrnete, bude mít stejná oprávnění pro každého zákazníka, který si plán zakoupí. Chcete-li přiřadit různé skupiny pro práci s každým zákazníkem, budete muset publikovat samostatný [soukromý plán,](../../marketplace/private-offers.md) který je výhradní pro každého zákazníka.

## <a name="create-your-offer"></a>Vytvořte si nabídku

1. Přihlaste se k [portálu partnerů cloudu](https://cloudpartner.azure.com/).
2. V levé navigační nabídce vyberte **Nová nabídka**a pak vyberte **Spravované služby**.
3. Zobrazí se oddíl **Editor** pro vaši nabídku se čtyřmi částmi, které můžete vyplnit: **Nastavení nabídky**, **Plány**, **Marketplace**a **Podpora**. Přečtěte si pokyny k dokončení těchto oddílů.

### <a name="enter-offer-settings"></a>Zadat nastavení nabídky

V části **Nastavení nabídky** zadejte následující:

|Pole  |Popis  |
|---------|---------|
|**ID nabídky**     | Jedinečný identifikátor vaší nabídky (v rámci profilu majitele stránek). Toto ID může obsahovat pouze malé alfanumerické znaky, pomlčky a podtržítka s maximálně 50 znaky. Nezapomeňte, že ID nabídky může být viditelné pro zákazníky na místech, jako jsou adresy URL produktů a fakturační sestavy. Po publikování nabídky nelze tuto hodnotu změnit.        |
|**Publisher ID**     | ID vydavatele, které bude přidruženo k nabídce. Pokud máte více než jedno ID vydavatele, můžete vybrat to, které chcete pro tuto nabídku použít.       |
|**Název**     | Název (až 50 znaků), který zákazníci uvidí pro vaši nabídku na Azure Marketplace a na webu Azure Portal. Použijte rozpoznatelnou značku, které zákazníci porozumí – pokud tuto nabídku propagujete prostřednictvím vlastního webu, nezapomeňte zde použít přesně stejný název.        |

Po dokončení vyberte **Uložit**. Nyní jste připraveni přejít do sekce **Plány.**

### <a name="create-plans"></a>Vytvořit plány

Každá nabídka musí mít jeden nebo více plánů (někdy označované jako sku. Můžete přidat více plánů pro podporu různých sad funkcí za různé ceny nebo přizpůsobit konkrétní plán pro omezené publikum konkrétních zákazníků. Zákazníci mohou zobrazit plány, které jsou k dispozici v rámci nadřazené nabídky.

V části Plány vyberte **Nový plán**. Poté zadejte **ID plánu**. Toto ID může obsahovat pouze malé alfanumerické znaky, pomlčky a podtržítka s maximálně 50 znaky. ID plánu může být viditelné pro zákazníky na místech, jako jsou adresy URL produktů a fakturační sestavy. Po publikování nabídky nelze tuto hodnotu změnit.

#### <a name="plan-details"></a>Podrobnosti plánu

V části **Podrobnosti o plánu** doplňte následující části:

|Pole  |Popis  |
|---------|---------|
|**Název**     | Popisný název plánu pro zobrazení. Maximální délka 50 znaků.        |
|**Souhrn**     | Stručný popis plánu pro zobrazení pod názvem. Maximální délka 100 znaků.        |
|**Popis**     | Text popisu, který poskytuje podrobnější vysvětlení plánu.         |
|**Model fakturace**     | Jsou zde zobrazeny 2 fakturační modely, ale musíte zvolit **Přineste si vlastní licenci** pro nabídky spravovaných služeb. To znamená, že zákazníkům budete přímo účtovat náklady spojené s touto nabídkou a společnost Microsoft vám neúčtuje žádné poplatky.   |
|**Je to soukromý plán?**     | Označuje, zda je skladová položka soukromá nebo veřejná. Výchozí hodnota je **Ne** (veřejné). Pokud tento výběr opustíte, váš plán nebude omezen na konkrétní zákazníky (nebo na určitý počet zákazníků); po publikování veřejného plánu jej později nelze změnit na soukromý. Chcete-li tento plán zpřístupnit pouze určitým zákazníkům, vyberte možnost **Ano**. Pokud tak učiníte, budete muset identifikovat zákazníky tím, že jim poskytnete ID předplatného. Ty lze zadat jeden po druhém (až pro 10 odběrů) nebo nahráním souboru .csv (až pro 20 000 předplatných). Nezapomeňte zde zahrnout vlastní předplatná, abyste mohli nabídku otestovat a ověřit. Další informace naleznete [v tématu Private SKU and Plans](../../marketplace/cloud-partner-portal-orig/cloud-partner-portal-azure-private-skus.md).  |

> [!IMPORTANT]
> Jakmile je plán publikován jako veřejný, nelze jej změnit na soukromý. Chcete-li určit, kteří zákazníci mohou vaši nabídku přijmout a delegovat prostředky, použijte soukromý plán. S veřejným tarifem nemůžete omezit dostupnost na určité zákazníky nebo dokonce na určitý počet zákazníků (i když můžete plán zcela ukončit, pokud se tak rozhodnete). Přístup [k delegování](onboard-customer.md#remove-access-to-a-delegation) můžete odebrat poté, co zákazník přijme nabídku, pouze pokud jste při publikování nabídky zahrnuli **autorizaci** s **definicí role** nastavenou na [přiřazení registrace spravovaných služeb delete.](../../role-based-access-control/built-in-roles.md#managed-services-registration-assignment-delete-role) Můžete také kontaktovat zákazníka a požádat ho o [odebrání přístupu](view-manage-service-providers.md#add-or-remove-service-provider-offers).

#### <a name="manifest-details"></a>Podrobnosti manifestu

Vyplňte část **Podrobnosti manifestu** pro váš plán. Tím se vytvoří manifest s informacemi o autorizaci pro správu zdrojů zákazníka. Tyto informace jsou nutné k povolení správy delegovaných prostředků Azure.

> [!NOTE]
> Jak je uvedeno výše, uživatelé a role v položkách **autorizace** se budou vztahovat na každého zákazníka, který si plán zakoupí. Pokud chcete omezit přístup na konkrétního zákazníka, budete muset publikovat soukromý plán pro jeho výhradní použití.

Nejprve zadejte **version** pro manifest. Použijte formát *n.n.n* (například 1.2.5).

Dále zadejte **ID klienta**. Toto je identifikátor GUID přidružený k ID klienta služby Azure Active Directory vaší organizace (tj. tenanta, se kterým budete pracovat při správě prostředků zákazníků). Pokud to nemáte po ruce, najdete ho tak, že najedete na název účtu na pravé horní straně portálu Azure nebo vyberete **switch adresáře**.

Nakonec do plánu přidejte jednu nebo více položek **autorizace.** Autorizace definují entity, které mají přístup k prostředkům a předplatným pro zákazníky, kteří zakoupí plán, a přiřazují role, které udělují určité úrovně přístupu.

> [!TIP]
> Ve většině případů budete chtít přiřadit oprávnění skupině uživatelů Azure AD nebo instančnímu objektu služby Azure AD, nikoli řadě jednotlivých uživatelských účtů. To umožňuje přidat nebo odebrat přístup pro jednotlivé uživatele, aniž by bylo nutné aktualizovat a znovu publikovat plán při změně požadavků na přístup. Další doporučení najdete [v tématu tenanty, role a uživatele ve scénářích Azure Lighthouse](../concepts/tenants-users-roles.md).

Pro každou **autorizaci**budete muset zadat následující. Potom můžete vybrat **Nové autorizace** tolikrát, kolikrát je potřeba přidat další uživatele a definice rolí.

- **ID objektu Azure AD**: Identifikátor Azure AD uživatele, skupiny uživatelů nebo aplikace, které budou udělena určitá oprávnění (jak je popsáno v definici role) prostředkům vašich zákazníků.
- **Zobrazovaný název objektu Azure AD**: Popisný název, který zákazníkovi pomůže pochopit účel této autorizace. Zákazník uvidí tento název při delegování prostředků.
- **Definice role**: Vyberte jednu z dostupných předdefinovaných rolí Azure AD ze seznamu. Tato role určí oprávnění, která uživatel v poli **ID objektu Azure AD** bude mít na prostředky vašich zákazníků. Popis těchto rolí najdete [v tématu předdefinované role](../../role-based-access-control/built-in-roles.md) a podpora rolí pro [správu delegovaných prostředků Azure](../concepts/tenants-users-roles.md#role-support-for-azure-delegated-resource-management).
  > [!NOTE]
  > Jako příslušné nové předdefinované role jsou přidány do Azure, budou k dispozici zde, i když může dojít k určité zpoždění, než se zobrazí.
- **Přiřaditelné role**: Tato možnost je vyžadována pouze v případě, že jste v **definici role** pro tuto autorizaci vybrali správce přístupu uživatelů. Pokud ano, musíte zde přidat jednu nebo více přiřaditelných rolí. Uživatel v poli **ID objektu Azure AD** bude moct přiřadit tyto **přiřaditelné role spravovaným** [identitám](../../active-directory/managed-identities-azure-resources/overview.md), které jsou nutné k [nasazení zásad, které lze napravit](deploy-policy-remediation.md). Všimněte si, že pro tohoto uživatele nebudou platit žádná další oprávnění, která jsou obvykle přidružena k roli Správce přístupu uživatelů. Pokud zde nevyberete jednu nebo více rolí, váš příspěvek neprojde certifikací. (Pokud jste pro definici role tohoto uživatele nevybrali správce přístupu uživatelů, nemá toto pole žádný vliv.)

> [!TIP]
> Chcete-li v případě potřeby [odebrat přístup k delegování,](onboard-customer.md#remove-access-to-a-delegation) zahrňte **autorizaci** s **definicí role** nastavenou na [roli odstranění přiřazení registrace spravovaných služeb](../../role-based-access-control/built-in-roles.md#managed-services-registration-assignment-delete-role). Pokud tato role není přiřazena, delegované prostředky mohou být odebrány pouze uživatelem v tenantovi zákazníka.

Po dokončení informací můžete vybrat **Nový plán** tolikrát, kolikrát potřebujete k vytvoření dalších plánů. Až budete hotovi, vyberte **Uložit**a pokračujte do sekce **Marketplace.**

### <a name="provide-marketplace-text-and-images"></a>Poskytnutí textu a obrázků marketplace

Část **Marketplace** je místo, kde můžete zadat text a obrázky, které zákazníci uvidí na Azure Marketplace a na webu Azure Portal.

V části **Přehled** doplňte následující pole:

|Pole  |Popis  |
|---------|---------|
|**Název**     |  Název nabídky, často dlouhý, formální název. Tento titul se zobrazí prominentně na trhu. Maximální délka 50 znaků. Ve většině případů by to mělo být stejné jako **jméno,** které jste zadali v části **Nastavení nabídky.**       |
|**Souhrn**     | Stručný účel nebo funkce vaší nabídky. To se obvykle zobrazí pod názvem. Maximální délka 100 znaků.        |
|**Dlouhý souhrn**     | Delší přehled účelu nebo funkce vaší nabídky. Maximální délka 256 znaků.        |
|**Popis**     | Více informací o vaší nabídce. Toto pole má maximální délku 3000 znaků a podporuje jednoduché formátování HTML. V popisu musíte uvést slova "spravovaná služba" nebo "spravované služby".       |
|**Identifikátor marketingu**     | Jedinečný identifikátor vhodný pro adresy URL. Tento identifikátor může obsahovat pouze malé alfanumerické znaky a pomlčky. Pro tuto nabídku bude použit v adresách URL marketplace. Pokud je například vaše ID *vydavatele contoso* a identifikátor *marketingu*je sampleApp *https://azuremarketplace.microsoft.com/marketplace/apps/contoso-sampleApp*, bude adresa URL vaší nabídky na Azure Marketplace .        |
|**Náhled ID předplatného**     | Přidejte jeden až 100 identifikátorů předplatného. Zákazníci přidružení k těmto předplatným budou moct zobrazit nabídku na Azure Marketplace, než se začne žít. Doporučujeme zde zahrnout vlastní předplatná, abyste si mohli prohlédnout, jak se vaše nabídka zobrazí na Azure Marketplace, než ji zpřístupníte zákazníkům.  (Týmy podpory a inženýrských týmů společnosti Microsoft budou také moci zobrazit vaši nabídku během tohoto období náhledu.)   |
|**Užitečné odkazy**     | Adresy URL související s vaší nabídkou, jako je dokumentace, poznámky k verzi, časté dotazy atd.        |
|**Doporučené kategorie (Max 5)**     | Jedna nebo více kategorií (až pět), které se vztahují k vaší nabídce. Tyto kategorie pomáhají zákazníkům zjistit vaši nabídku na Azure Marketplace a na webu Azure Portal.        |

V části **Marketingové artefakty** můžete nahrát loga a další datové zdroje, které se zobrazí s vaší nabídkou. Volitelně můžete nahrát snímky obrazovky nebo odkazy na videa, které zákazníkům pomohou pochopit vaši nabídku.

Jsou vyžadovány čtyři velikosti loga: **Malé (40x40),** **Střední (90x90),** **Velké (115x115)** a **Široké (255x115)**. Pro loga postupujte podle těchto pokynů:

- Design Azure má jednoduchou paletu barev. Omezte počet primárních a sekundárních barev ve vašem logu.
- Barvami motivů na portálu jsou bílá a černá. Nepoužívejte tyto barvy jako barvu pozadí pro vaše logo. Použijte barvu, která vaše logo na portálu zvýrazní. Doporučujeme použít jednoduché primární barvy.
- Pokud použijete průhledné pozadí, ujistěte se, že text ani logo nejsou bílé, černé nebo modré.
- Logo by mělo působit plochým dojmem a nemělo by obsahovat barevné přechody. Nepoužívejte v logu pozadí s barevným přechodem.
- Na logo neumisťujte žádný text, a to ani název vaší společnosti nebo název značky.
- Zajistěte, aby logo nebylo roztažené.

Logo **Hero (815x290)** je volitelné, ale doporučujeme. Pokud uvedete logo hrdiny, postupujte podle těchto pokynů:

- Neuvádějte žádný text v logu hrdiny a nezapomeňte ponechat 415 pixelů prázdného místa na pravé straně loga. To je nutné ponechat prostor pro textové prvky, které budou vloženy programově: zobrazovaný název vydavatele, název plánu, nabídka dlouhéshrnutí.
- Pozadí loga hrdiny nemusí být černé, bílé nebo průhledné. Zkontrolujte, zda barva pozadí není příliš světlá, protože vložený text bude zobrazen bíle.
- Jakmile nabídku publikujete s ikonou hrdiny, nemůžete ji odebrat (i když ji můžete v případě potřeby aktualizovat jinou verzí).

V části **Správa zájemců** můžete vybrat systém CRM, do kterého budou vaši zájemci uloženi. Všimněte si, že podle [certifikačních zásad spravovaných služeb](https://docs.microsoft.com/legal/marketplace/certification-policies#700-managed-services)je vyžadován **cíl zájemce.**

A konečně uveďte **adresu URL zásad ochrany osobních údajů** a podmínky **použití** v části **Právní.** Zde můžete také určit, zda chcete pro tuto nabídku použít [standardní smlouvu.](../../marketplace/standard-contract.md)

Nezapomeňte uložit změny před přechodem na část **Podpora.**

### <a name="add-support-info"></a>Přidání informací o podpoře

V části **Podpora** zadejte jméno, e-mail a telefonní číslo technického kontaktu a kontaktu zákaznické podpory. Budete také muset poskytnout adresy URL podpory. Společnost Microsoft může tyto informace použít, když vás potřebujeme kontaktovat ohledně problémů s podnikáním a podporou.

Po přidání těchto informací vyberte **Uložit.**

## <a name="publish-your-offer"></a>Publikování nabídky

Po dokončení všech částí je dalším krokem publikování nabídky na Azure Marketplace. Výběrem tlačítka **Publikovat** zahájíte proces vytváření nabídky živě. Další informace o tomto procesu najdete [v tématu publikování nabídek Azure Marketplace a AppSource](../../marketplace/cloud-partner-portal/manage-offers/cpp-publish-offer.md).

[Aktualizovanou verzi nabídky](../../marketplace/cloud-partner-portal/manage-offers/cpp-update-offer.md) můžete kdykoli publikovat. Můžete například přidat novou definici role do dříve publikované nabídky. Když tak učiníte, zákazníci, kteří už přidali nabídku, uvidí na stránce [**Poskytovatelé služeb**](view-manage-service-providers.md) na webu Azure Portal ikonu, která jim dá vědět, že je k dispozici aktualizace. Každý zákazník bude moci [zkontrolovat změny](view-manage-service-providers.md#update-service-provider-offers) a rozhodnout, zda chce aktualizovat na novou verzi. 

## <a name="the-customer-onboarding-process"></a>Proces registrace zákazníka

Poté, co zákazník přidá vaši nabídku, bude moct [delegovat jedno nebo více konkrétních předplatných nebo skupin prostředků](view-manage-service-providers.md#delegate-resources), které pak budou na palubě pro správu delegovaných prostředků Azure. Pokud zákazník přijal nabídku, ale ještě nedelegoval žádné prostředky, zobrazí se mu poznámka v horní části stránky **Poskytovatelé** [**služeb**](view-manage-service-providers.md) na webu Azure Portal.

> [!IMPORTANT]
> Delegování musí být provedeno nehostem v tenantovi zákazníka, který má [předdefinovanou roli vlastníka](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#owner) pro předplatné, které je na palubě (nebo který obsahuje skupiny prostředků, které jsou na palubě). Chcete-li zobrazit všechny uživatele, kteří mohou delegovat předplatné, uživatel v tenantovi zákazníka můžete vybrat předplatné na webu Azure Portal, otevřete **řízení přístupu (IAM)** a [zobrazit všechny uživatele s rolí Vlastník](../../role-based-access-control/role-assignments-list-portal.md#list-owners-of-a-subscription).

Jakmile zákazník deleguje předplatné (nebo jednu nebo více skupin prostředků v rámci předplatného), poskytovatel prostředků **Microsoft.ManagedServices** bude pro toto předplatné zaregistrován a uživatelé ve vašem tenantovi budou mít přístup k delegovaným prostředkům podle autorizací ve vaší nabídce.

## <a name="next-steps"></a>Další kroky

- Další informace o [komerčním tržišti](../../marketplace/partner-center-portal/commercial-marketplace-overview.md).
- Další informace o [prostředích správy mezi tenanty](../concepts/cross-tenant-management-experience.md).
- [Zobrazte a spravujte zákazníky](view-manage-customers.md) tak, že přejdete na můj **zákazník** na webu Azure Portal.
