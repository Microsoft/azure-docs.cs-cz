---
title: zahrnout soubor
description: zahrnout soubor
documentationcenter: partner-center-commercial-marketplace
author: qianw211
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: include
ms.date: 08/13/2019
ms.author: dsindona
ms.custom: include file
ms.openlocfilehash: f2446a924ceed37c51779efc9d9e94c0252a2067
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80321774"
---
Karta **Testovací jednotka** umožňuje nastavit ukázku (nebo "testovací jízdu"), která zákazníkům umožní vyzkoušet si vaši nabídku před potvrzením nákupu. Další informace naleznete v článku [Co je testovací jízda?](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/what-is-test-drive) Pokud již nechcete poskytovat zkušební jízdu pro vaši nabídku, vraťte se na stránku **Nastavení nabídky** a odškrtněte **možnost Povolit testovací jízdu**.

### <a name="technical-configuration"></a>Technická konfigurace
K dispozici jsou následující typy testovacích jednotek, z nichž každý má své vlastní požadavky na technickou konfiguraci.

- [Azure Resource Manager](#technical-configuration-for-azure-resource-manager-test-drive)
- [Dynamics 365](#technical-configuration-for-dynamics-365-test-drive)
- [Aplikace logiky](#technical-configuration-for-logic-app-test-drive)
- [Power BI](#technical-configuration-not-required-for-power-bi-test-drives) (Technická konfigurace není nutná)

#### <a name="technical-configuration-for-azure-resource-manager-test-drive"></a>Technická konfigurace pro testovací jednotku Azure Resource Manager

Šablona nasazení, která obsahuje všechny prostředky Azure, které tvoří vaše řešení. Produkty, které odpovídají tomuto scénáři, používají jenom prostředky Azure. Přečtěte si další informace o nastavení [testovací jednotky Azure Resource Manager](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/azure-resource-manager-test-drive).

- **Oblasti** (povinné): V současné době existuje 26 oblastí podporovaných Azure, kde můžete zpřístupnit testovací jednotku. Obvykle budete chtít, aby vaše testovací jednotky k dispozici v oblastech, kde očekáváte největší počet zákazníků, aby mohli vybrat nejbližší oblast pro nejlepší výkon. Budete se muset ujistit, že vaše předplatné je povoleno nasadit všechny prostředky potřebné v každé z oblastí, které vybíráte.

- Instance : **Vyberte**typ (horký nebo studený) a počet dostupných instancí, které se vynásobí počtem oblastí, kde je vaše nabídka k dispozici.

**Hot**: Tento typ instance je nasazen a čeká na přístup pro vybranou oblast. Zákazníci mohou okamžitě přistupovat *k hot* instance testovací jednotky, spíše než muset čekat na nasazení. Kompromis emituje, že tyto instance jsou vždy spuštěné ve vašem předplatném Azure, takže jim vzniknou vyšší náklady na provozuna. Důrazně doporučujeme mít alespoň jednu *instanci Hot,* protože většina zákazníků nechce čekat na úplné nasazení, což má za následek pokles v použití zákazníka, pokud není k dispozici žádná instance *Hot.*

**Studená**: Tento typ instance představuje celkový počet instancí, které lze případně nasadit na oblast. Studené instance vyžadují celou šablonu Správce prostředků testovací jednotky k nasazení, když zákazník požaduje testovací jednotku, takže *instance Cold* jsou mnohem pomalejší než instance *Hot.* Kompromis je, že stačí zaplatit po dobu trvání testovací jednotky, *není* vždy spuštěna na předplatné Azure jako s *hot* instance.

- **Testovací jednotka Azure Resource Manager šablona:** Nahrajte .zip obsahující šablonu Azure Resource Manager.  Další informace o vytvoření šablony Azure Resource Manager v článku úvodního článku [Vytvoření a nasazení šablon Azure Resource Manageru pomocí portálu Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal).

- **Doba trvání zkušební jednotky** (povinné): Zadejte dobu, po kterou bude testovací jízda aktivní, v počtu hodin. Testovací cesta se automaticky ukončí po uplynutí tohoto časového období. Tato doba může být stanovena pouze o celý počet hodin (např. "2" hodiny, "1,5" není platný).

#### <a name="technical-configuration-for-dynamics-365-test-drive"></a>Technická konfigurace pro testovací jízdu Dynamics 365

Společnost Microsoft může odebrat složitost nastavení testovací jednotky hostováním a údržbou zřizování a nasazování služeb pomocí tohoto typu testovací jednotky. Konfigurace pro tento typ hostované testovací jednotky je stejná bez ohledu na to, zda testovací jednotka cílí na cílovou skupinu Business Central, Customer Engagement nebo Operations.

- **Maximální počet souběžných testovacích jednotek** (povinné): Nastavte maximální počet zákazníků, kteří mohou testovací jednotku používat najednou. Každý souběžný uživatel bude využívat licenci Dynamics 365, když je testovací jednotka aktivní, takže budete muset zajistit, že máte k dispozici dostatek licencí pro podporu nastaveného maximálního limitu. Doporučená hodnota 3-5.

- **Doba trvání zkušební jízdy** (povinné): Zadejte dobu, po kterou bude testovací jednotka aktivní, definováním počtu hodin. Po uplynutí této doby relace skončí a již nebude spotřebovávat jednu z vašich licencí. Doporučujeme hodnotu 2-24 hodin v závislosti na složitosti vaší nabídky. Tato doba může být stanovena pouze o celý počet hodin (např. "2" hodiny, "1,5" není platný).  Uživatel může požádat o novou relaci, pokud jim dojde čas a chcete znovu získat přístup k testovací jednotce.

- **Adresa URL instance** (povinné): Adresa URL, na které zákazník zahájí testovací jízdu. Adresa URL instance Dynamics 365, ve které aplikace běží s `https://testdrive.crm.dynamics.com`nainstalovanými ukázkovými daty (např. ).

- **Adresa URL webového rozhraní API** instance (povinné): Načtěte adresu URL webového rozhraní API pro instanci Dynamics 365 tak, že se přihlásíte ke svému účtu Microsoft 365 a přejdete do **nastavení** \&gt; **Přizpůsobení** \&gt; **Zdroje pro vývojáře** \&gt; **Instance Web API (Adresa URL root služby)**, `https://testdrive.crm.dynamics.com/api/data/v9.0`zkopírujte adresu URL, která je k dispozici zde (např. ).

- **Název role** (povinné): Zadejte název role zabezpečení, který jste definovali ve vlastní testovací jednotce Dynamics 365. To bude přiřazeno uživateli během jeho zkušební jízdy (např. test-drive-role).

#### <a name="technical-configuration-for-logic-app-test-drive"></a>Technická konfigurace testovací jednotky aplikace Logika

Všechny vlastní produkty by měly používat tento typ šablony nasazení testovací jednotky, která zahrnuje celou řadu složitých architektur řešení. Další informace o nastavení testovacích jednotek aplikace Logika najdete na webu [Operace](https://github.com/Microsoft/AppSource/blob/master/Setup-your-Azure-subscription-for-Dynamics365-Operations-Test-Drives.md) a [Zapojení zákazníků na](https://github.com/Microsoft/AppSource/wiki/Setting-up-Test-Drives-for-Dynamics-365-app) GitHubu.

- **Oblast** (povinný rozevírací seznam s jedním výběrem): Momentálně existuje 26 oblastí podporovaných Azure, kde můžete zpřístupnit testovací jednotku. Prostředky pro vaši aplikaci logiky se nasadí v oblasti, kterou vyberete. Pokud vaše aplikace logiky obsahuje vlastní prostředky uložené v určité oblasti, ujistěte se, že oblast je vybrána zde. Nejlepší způsob, jak to udělat, je plně nasadit aplikaci logiky místně na vašem předplatném Azure na portálu a ověřit, že funguje správně před provedením tohoto výběru.

- **Maximální počet souběžných testovacích jednotek** (povinné): Nastavte maximální počet zákazníků, kteří mohou testovací jednotku používat najednou. Tyto testovací jednotky jsou již nasazeny, což zákazníkům umožňuje okamžitý přístup k nim bez čekání na nasazení.

- **Doba trvání zkušební jednotky** (povinné): Zadejte dobu, po kterou bude testovací jízda aktivní, v počtu hodin. Testovací jednotka se automaticky ukončí po uplynutí tohoto časového období.

- **Název skupiny prostředků Azure** (povinné): Zadejte název [skupiny prostředků Azure,](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#resource-groups) kde se uloží testovací jednotka aplikace Logika.

- **Název aplikace logiky Azure** (povinné): Zadejte název aplikace logiky, která přiřadí testovací jednotku uživateli. Tato aplikace logiky musí být uložena ve skupině prostředků Azure výše.

- **Deprovision název aplikace logiky** (povinné): Zadejte název aplikace logiky, která deprovisions testovací jednotky po dokončení zákazníka. Tato aplikace logiky musí být uložena ve skupině prostředků Azure výše.

#### <a name="technical-configuration-not-required-for-power-bi-test-drives"></a>Technická konfigurace není vyžadována pro testovací jízdy Power BI

Produkty, které chtějí předvést interaktivní vizuál Power BI, můžou pomocí vloženého odkazu sdílet vlastní řídicí panel jako testovací jízdu, bez nutnosti další technické konfigurace. Přečtěte si další informace o nastavení aplikací šablon[Power BI.](https://docs.microsoft.com/power-bi/service-template-apps-overview)

### <a name="deployment-subscription-details"></a>Podrobnosti o předplatném nasazení

Chcete-li nasadit testovací disk vaším jménem, vytvořte a poskytněte samostatné jedinečné předplatné Azure. (Není vyžadováno pro testovací jednotky Power BI).

- **ID předplatného Azure** (vyžadováno pro aplikace Azure Resource Manager a Logic): Zadejte ID předplatného, které udělí přístup ke službám účtu Azure pro vytváření sestav a fakturaci využití prostředků. Doporučujeme zvážit [vytvoření samostatného předplatného Azure,](https://docs.microsoft.com/azure/billing/billing-create-subscription) které se použije pro testovací jednotky, pokud ho ještě nemáte. ID předplatného Azure najdete tak, že se přihlásíte na [portál Azure](https://portal.azure.com/) a přejdete na kartu **Předplatná** v nabídce na levé straně. Výběrem karty se zobrazí Vaše ID předplatného (např. "a83645ac-1234-5ab6-6789-1h234g764ghty").

- **ID klienta Azure AD** (povinné): Zadejte [ID klienta](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in)služby Azure Active Directory (AD). Chcete-li toto ID najít, přihlaste se na [portál Azure](https://portal.azure.com/), v levé nabídce vyberte kartu Active Directory a vyberte **vlastnosti** a vyhledejte uvedené číslo **ID adresáře** (například 50c464d3-4930-494c-963c-1e951d15360e). ID klienta vaší organizace můžete také vyhledat pomocí adresy [https://www.whatismytenantid.com](https://www.whatismytenantid.com)URL názvu domény na adrese: .

- **Název klienta Azure AD** (vyžadováno pro Dynamic 365): Zadejte název služby Azure Active Directory (AD). Chcete-li najít tento název, přihlaste se na [portál Azure](https://portal.azure.com/), v pravém horním rohu název klienta bude uveden pod názvem vašeho účtu.

- **ID aplikace Azure AD** (povinné): Zadejte [ID aplikace](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in)služby Azure Active Directory (AD). Chcete-li toto ID najít, přihlaste se na [portál Azure](https://portal.azure.com/), v levé nabídce vyberte kartu Active Directory, vyberte **Registrace aplikací**a vyhledejte uvedené číslo **ID aplikace** (například 50c464d3-4930-494c-963c-1e951d15360e).

- **Tajný klíč klienta aplikace Azure AD** (povinné): Zadejte [tajný klíč klienta](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#certificates-and-secrets)aplikace Azure AD . Chcete-li najít tuto hodnotu, přihlaste se na [portál Azure](https://portal.azure.com/). V levé nabídce vyberte kartu **Azure Active Directory,** vyberte **Registrace aplikací**a vyberte aplikaci testovací jednotky. Dále vyberte **Certifikáty a tajné kódy**, vyberte **Nový tajný klíč klienta**, zadejte popis, v **části** **Nikdy** nevyprší platnost , a pak zvolte **Přidat**. Ujistěte se, že zkopírujete hodnotu. (Neprocházejte se od stránky dříve, než to uděláte, jinak nebudete mít přístup k hodnotě.)

Nezapomeňte **uložit** před přechodem na další sekci!

### <a name="test-drive-listings-optional"></a>Výpisy zkušebních jízd (volitelné)

Možnost **Test Drive výpisy** nakartě **Test drive** zobrazuje jazyky (a trhy), kde je vaše testovací jednotka k dispozici, v současné době angličtina (Spojené státy) je jediné umístění k dispozici. Kromě toho tato stránka zobrazuje stav zápisu specifického pro jazyk a datum a čas, kdy byl přidán. Pro každý jazyk/trh budete muset definovat podrobnosti o testovací jízdě (popis, uživatelská příručka, videa atd.).

- **Popis** (povinné): Popište zkušební jízdu, co bude prokázáno, cíle pro uživatele experimentovat s, funkce k prozkoumání a veškeré relevantní informace, které pomohou uživateli určit, zda získat vaši nabídku. Do tohoto pole lze zadat až 3 000 znaků textu. 

- **Přístup k informacím** (vyžadováno pro testovací jednotky Azure Resource Manager a Logic): Vysvětlete, co zákazník potřebuje vědět, aby mohl přistupovat k této testovací jednotce a používat ji. Projděte si scénář pro použití vaší nabídky a přesně to, co by měl zákazník vědět pro přístup k funkcím v průběhu testovací jízdy. Do tohoto pole lze zadat až 10 000 znaků textu.

- **Uživatelská příručka** (povinné): Podrobný návod k testovací jízdě. Uživatelská příručka by měla zahrnovat přesně to, co chcete, aby zákazník získal z testovací jízdy, a sloužit jako reference pro všechny otázky, které mohou mít. Soubor musí být ve formátu PDF a po nahrání musí být pojmenován (maximálně 255 znaků).

- **Videa: Přidejte videa** (volitelně): Videa lze nahrát na YouTube nebo Vimeo a odkazovat zde s odkazem a miniaturou obrázku (533 x 324 pixelů), aby si zákazník mohl prohlédnout procházku informací, které jim pomohou lépe porozumět testovací jízdě, včetně toho, jak úspěšně využívat funkce vaší nabídky a porozumět scénářům, které zvýrazňují jejich výhody.
  - **Jméno** (povinné)
  - **URL (pouze YouTube nebo Vimeo)** (povinné)
  - **Miniatura (533 x 324 px):** Soubor obrázku musí být ve formátu PNG.

Po dokončení těchto polí vyberte **Uložit.**
