---
title: Vyzkoušejte Azure Resource Manageru | Dokumentace Microsoftu
description: Sestavení Markeplace testovací jízdu a pomocí Azure Resource Manageru
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: pbutlerm
manager: Patrick .Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: pbutlerm
ms.openlocfilehash: 87e57a1ee417a4cc69308e8af80dd9b781d60aaf
ms.sourcegitcommit: 78ec955e8cdbfa01b0fa9bdd99659b3f64932bba
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/10/2018
ms.locfileid: "53139261"
---
<a name="azure-resource-manager-test-drive"></a>Vyzkoušejte Azure Resource Manageru
=================================

Tento článek je určený pro vydavatele, kteří mají jejich nabídek zveřejněných na webu Azure Marketplace nebo kteří jsou v AppSource, ale má být sestaveno jejich testovací verze s pouze prostředky Azure.

Šablonu Azure Resource Manageru (Azure Resource Manager) je programový kontejner prostředků Azure navrhnout pro představují doporučené řešení. Pokud nejste obeznámeni s tím, jaké šablony Resource Manageru je, přečtěte si o [pochopení šablony ARM](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) a [vytváření šablon ARM](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authoring-templates) k Ujistěte se, že víte, jak vytvářet a testovat vlastní šablony.

Co dělá Test Drive je, že přijímá zadané šablony Resource Manageru a umožňuje nasazení všech prostředků potřebných z této šablony Resource Manageru do skupiny prostředků.

Pokud budete chtít vytvořit vyzkoušejte Azure Resource Manageru, jsou požadavky můžete:

- Sestavení, testování a pak nahrajte vaši šablonu Test Drive Resource Manageru.
- Nakonfigurujte všechna požadovaná metadata a nastavení, aby mohla vaše testovací verze.
- Publikujte vaši nabídku s testovací verze povolena.

<a name="how-to-build-an-azure-resource-manager-test-drive"></a>Jak vytvořit vyzkoušejte Azure Resource Manageru
------------------------------

Nejdůležitější část o vytvoření Azure Resource Manageru Test Drive je určit, jaké scénáře mají vaši zákazníci práce. Je, že brána firewall a chcete je ukázka, jak dobře zpracování útoků prostřednictvím injektáže skriptu? Se, že je produkt úložiště a chcete ukázka jak rychlé a snadné řešení komprimuje soubory?

Ujistěte se, ručitel strávit dostatečné množství času hodnocení, jaké jsou nejlepší způsoby předvést váš produkt. Konkrétně kolem všechny požadované prostředky budete potřebovat, jako je navrhování dostatečně jednodušší šablony Resource Manageru.

Chcete-li pokračovat v našem příkladu brány firewall, může být architektury, že budete potřebovat veřejnou IP adresu URL pro vaši službu a jinou veřejnou IP adresu URL webu, který chrání vaše brána firewall. Každý IP je nasazené ve virtuálním počítači a společně se skupinu zabezpečení sítě a síťové rozhraní připojené.

Jakmile je návrh balíček požadovaných prostředků, nyní zahrnuje psaní a vytváření testů jednotek Resource Manageru šablony.

<a name="writing-test-drive-resource-manager-templates"></a>Zápis testů jednotek Resource Manageru šablony
--------------------------------

Testovací verze spustí nasazení v plně automatizované režimu a proto, testovací verze šablony mají určitá omezení je popsáno níže.

### <a name="parameters"></a>Parametry

Většina šablony mají sadu parametrů. Parametry definovat názvy prostředků, velikosti prostředky (například typy účtů úložiště nebo velikosti virtuálních počítačů), uživatelská jména a hesla, názvy DNS a tak dále. Když nasadíte řešení s využitím webu Azure portal, můžete ručně naplnění těchto parametrů, vyberte dostupné názvy DNS nebo názvy účtů úložiště a tak dále.

![Seznam parametrů v Azure Resource Manageru](./media/azure-resource-manager-test-drive/param1.png)

Testovací verze však funguje v režimu plně automatická, bez zásahu člověka, tak podporuje pouze omezená sada parametr kategorie. Pokud parametr v šabloně testovacího správce prostředků jednotky není spadají do jedné z podporovaných kategorií, je nutné **nahraďte tento parametr s hodnotou proměnné nebo konstantní.**

Můžete použít libovolný platný název pro parametry, vyzkoušejte rozpozná parametr kategorie pomocí metadat typu hodnoty. Můžete **musíte zadat typ metadat pro každý parametr šablony**, jinak nebude šablony projít ověřením:

    "parameters": {
      ...
      "username": {
        "type": "string",
        "metadata": {
          "type": "username"
        }
      },
      ...
    }

Je také důležité si uvědomit, že **všechny parametry jsou volitelné**, pokud nepoužíváte\'t chcete použít, vám\'nemusíte.

### <a name="accepted-parameter-metadata-types"></a>Typy parametru přijatý metadat

| Typ metadat   | Typ parametru  | Popis     | Ukázková hodnota    |
|---|---|---|---|---|
| **BaseURI**     | řetězec          | Základní identifikátor URI balíčku nasazení| [https://\<\..\>.blob.core.windows.net/\<\..\>](#) |
| **uživatelské jméno**    | řetězec          | Nový náhodný uživatelské jméno.| admin68876      |
| **Heslo**    | zabezpečený řetězec    | Nový náhodné heslo | LP! ACS\^2kh     |
| **Id relace**   | řetězec          | Testovací verze relace jedinečný Identifikátor (GUID)    | b8c8693e-5673-449c-badd-257a405a6dee |

#### <a name="username"></a>uživatelské jméno

Tento parametr se inicializuje testovací verze **základní identifikátor Uri** balíčku nasazení, takže tento parametr slouží k vytváření identifikátorů Uri jakéhokoli souboru zahrnuté do balíčku.

    "parameters": {
      ...
      "baseuri": {
        "type": "string",
        "metadata": {
          "type": "baseuri",
          "description": "Base Uri of the deployment package."
        }
      },
      ...
    }

Uvnitř šablony můžete použít tento parametr k vytváření identifikátorů Uri jakéhokoli souboru z balíčku pro nasazení vaší testovací verze. Následující příklad ukazuje, jak vytvořit identifikátor Uri propojené šablony:

    "templateLink": {
      "uri": "[concat(parameters('baseuri'),'templates/solution.json')]",
      "contentVersion": "1.0.0.0"
    }

#### <a name="username"></a>uživatelské jméno

Testovací verze inicializuje tento parametr s novým názvem náhodné uživatele:

    "parameters": {
      ...
      "username": {
        "type": "string",
        "metadata": {
          "type": "username",
          "description": "Solution admin name."
        }
      },
      ...
    }

Hodnota vzorku:

    admin68876

Náhodné nebo konstantní uživatelská jména můžete pro vaše řešení.

#### <a name="password"></a>heslo

Testovací verze inicializuje tohoto parametru s novou náhodné heslo:

    "parameters": {
      ...
      "password": {
        "type": "securestring",
        "metadata": {
          "type": "password",
          "description": "Solution admin password."
        }
      },
      ...
    }

Hodnota vzorku:

    Lp!ACS^2kh

Náhodné nebo konstantní hesla můžete použít pro vaše řešení.

#### <a name="session-id"></a>ID relace

Testovací verze inicializovat tento parametr s jedinečný identifikátor GUID představující ID relace testovací verze:

    "parameters": {
      ...
      "sessionid": {
        "type": "string",
        "metadata": {
          "type": "sessionid",
          "description": "Unique Test Drive session id."
        }
      },
      ...
    }

Hodnota vzorku:

    b8c8693e-5673-449c-badd-257a405a6dee

Tento parametr slouží k jednoznačné identifikaci Test Drive relace, pokud je nutné.

### <a name="unique-names"></a>Jedinečné názvy

Některé prostředky Azure, jako jsou účty úložiště nebo názvy DNS, vyžaduje globálně jedinečné názvy.

To znamená, že pokaždé, když se Test Drive nasadí šablony Resource Manageru, vytváří **novou skupinu prostředků s jedinečným názvem** pro všechny jeho\' prostředky. Proto je potřeba použít [uniquestring](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-functions#uniquestring) funkce, které jsou spojeny s názvy proměnných ve skupině prostředků. ID pro generování náhodných jedinečné hodnoty:

      "variables": {
      ...
      "domainNameLabel": "[concat('contosovm',uniquestring(resourceGroup().id))]",
      "storageAccountName": "[concat('contosodisk',uniquestring(resourceGroup().id))]",
      ...
    }

Ujistěte se, že zřetězí vašeho parametru nebo proměnnou řetězce (\'contosovm\') s výstupem jedinečný řetězec (\'resourceGroup () .id\'), protože toto zaručuje jedinečnost a spolehlivost každou proměnnou.

Například většina názvy prostředků nemůže začínat číslicí, ale jedinečnou funkci řetězce může vrátit řetězec, který začíná znakem číslici. Takže pokud používáte výstupní nezpracovaná jedinečný řetězec, nasazení se nezdaří. 

Můžete najít další informace o pravidlech pojmenování prostředků a omezení v [v tomto článku](https://docs.microsoft.com/azure/guidance/guidance-naming-conventions).

### <a name="deployment-location"></a>Umístění nasazení

Můžete zpřístupnit můžete vyzkoušet testovací jízdu v různých oblastech Azure. Cílem je umožnit uživateli vybrat nejbližší oblasti, aby poskytovaly touchdown uživatelské prostředí.

Když Test Drive vytvoří instanci tohoto prostředí, vždy vytvoří skupinu prostředků v oblasti zvolit uživatelem a pak spustí vaši šablonu nasazení v tomto kontextu skupiny. Šabloně byste tedy vybrat umístění nasazení ze skupiny prostředků:

    "variables": {
      ...
      "location": "[resourceGroup().location]",
      ...
    }

A pak pro každý prostředek pro konkrétní instance testovacího prostředí používá toto umístění:

    "resources": [
      {
        "type": "Microsoft.Storage/storageAccounts",
        "location": "[variables('location')]",
        ...
      },
      {
        "type": "Microsoft.Network/publicIPAddresses",
        "location": "[variables('location')]",
        ...
      },
      {
        "type": "Microsoft.Network/virtualNetworks",
        "location": "[variables('location')]",
        ...
      },
      {
        "type": "Microsoft.Network/networkInterfaces",
        "location": "[variables('location')]",
        ...
      },
      {
        "type": "Microsoft.Compute/virtualMachines",
        "location": "[variables('location')]",
        ...
      }
    ]

Potřebujete, abyste měli jistotu, že vaše předplatné může nasadit všechny prostředky, které chcete nasadit ve všech oblastech, které vyberete. Také budete muset ověřit, že vaše Image virtuálních počítačů jsou k dispozici ve všech oblastech, které chcete povolit, jinak nebude fungovat šablony nasazení pro některé oblasti.

### <a name="outputs"></a>Výstupy

Obvykle se šablonami Resource Manageru můžete nasadit bez neprodukuje žádný výstup. Důvodem je, že znáte všech hodnot, které můžete použít k naplnění parametry šablony a vždy ručně si můžete prohlédnout vlastnosti libovolný prostředek.

Pro šablony Resource Manageru jednotky testů však ho\'s důležité se vraťte do testovací verze všechny informace, které je požadováno pro získání přístupu k testovacímu prostředí (identifikátory URI webu, virtuální počítač hostitele, uživatelská jména a hesla). Ujistěte se, že všechny výstupní názvy nejsou čitelné, protože tyto proměnné jsou uvedeny na zákazníka.

Nejsou žádná omezení související s výstupů šablony. Jenom nezapomeňte vyzkoušet testovací jízdu převede všechny výstupní hodnoty do **řetězce**, takže pokud je objekt posílat výstup, uživateli se zobrazí JSON řetězec.

Příklad:

    "outputs": {
      "Host Name": {
        "type": "string",
        "value": "[reference(variables('pubIpId')).dnsSettings.fqdn]"
      },
      "User Name": {
        "type": "string",
        "value": "[parameters('adminName')]"
      },
      "Password": {
        "type": "string",
        "value": "[parameters('adminPassword')]"
      }
    }

### <a name="subscription-limits"></a>Limity předplatného

Ještě jedna věc byste měli vzít v úvahu je předplatné a omezení služby. Například pokud chcete nasadit až deset 4jádrový virtuální počítače, musíte zajistit, aby předplatné, můžete použít pro testovací prostředí umožňuje používat 40 jader.

Můžete najít další informace o předplatném Azure a omezení služby [v tomto článku](https://docs.microsoft.com/azure/azure-subscription-service-limits). Jak více jednotek testů lze provést ve stejnou dobu, ověřte, že vaše předplatné může zpracovávat \# jader vynásobené celkovým počtem souběžných Test jednotky, dá se přenést.

### <a name="what-to-upload"></a>Co se má nahrát

Nahrání šablony Resource Manageru jednotky testů jako soubor zip, který může obsahovat různé artefakty nasazení, ale musí mít jeden soubor s názvem **hlavní template.json**. Tento soubor je nasazení šablony Azure Resource Manageru a testovací verze se používá k vytvoření instance testovacího prostředí.

Pokud máte další prostředky nad rámec tohoto souboru, můžete využít jako externí prostředek do šablony nebo můžete zahrnout zdroje v souboru zip.

Během publikování certifikace Test Drive unzips balíček pro nasazení a její obsah umístí do kontejneru objektů blob interní Test Drive. Struktura kontejneru strukturu balíček pro nasazení:

| Package.zip                       | Test kontejneru objektů blob disku         |
|---|---|
hlavní template.json                | [https://\<\.... \>.blob.core.windows.net/\<\.... \>/main-template.json](#)  |
 Templates/Solution.JSON           | [https://\<\.... \>.blob.core.windows.net/\<\.... \>/templates/solution.json](#) |
| Scripts/warmup.ps1                | [https://\<\.... \>.blob.core.windows.net/\<\.... \>/scripts/warmup.ps1](#)  |


Označujeme je identifikátor Uri tento kontejner objektů blob základní identifikátor Uri. Každou revizí testovacího prostředí má svůj vlastní kontejner objektů blob, a proto každou revizí testovacího prostředí má svůj vlastní základní identifikátor Uri. Testovací verze můžete předat základní identifikátor Uri balíčku rozzipovaný nasazení do šablony prostřednictvím parametrů šablony.

<a name="transforming-template-examples-for-test-drive"></a>Transformace v příkladech šablon pro testovací verze
---------------------------------------------

Proces zapnutí architekturu prostředků do šablony Resource Manageru jednotky testů může být složitý. Aby bylo možné pomohou lépe tohoto procesu jsme\'uložit provedené příklady o tom, aby se co nejlépe [transformovat aktuální šablony nasazení zde](./transforming-examples-for-test-drive.md).

<a name="how-to-publish-a-test-drive"></a>Jak publikovat testovací verze
---------------------------

Teď, když máte vaše testovací verze vytvořené, tato část vás provede každé pole, které potřebujete k úspěšnému publikování vaše testovací verze.

![Povolení testovací verze v uživatelském rozhraní](./media/azure-resource-manager-test-drive/howtopub1.png)

Je toto první a nejdůležitější pole můžete určit, jestli chcete, aby Test Drive, nebo není povolená pro vaši nabídku. Když vyberete **Ano,** rest s všechna požadovaná pole ve formuláři se zobrazí pro vás k vyplnění. Když vyberete **Ne,** formuláře bude zakázáno a opětovné publikování pomocí testovací verze zakázán, vaše testovací verze se odebere z produkčního prostředí.

Poznámka: Pokud jsou všechny testy jednotek aktivně používá uživatelů, tyto testovací verze bude nadále spuštěna do vypršení platnosti jejich relace.

### <a name="details"></a>Podrobnosti

Další části a vyplňte je že nabízejí podrobné informace o vaše testovací verze.

![Testovací verze podrobné informace](./media/azure-resource-manager-test-drive/howtopub2.png)

**Popis –** *vyžaduje* Toto je místo, kde píšete hlavní popis o tom, co je na vaše testovací verze. Zákazník Sem přijde na jaké scénáře vaše testovací verze se zahrnující informace o tomto produktu. 

**Uživatelská příručka -** *vyžaduje* Toto je podrobný návod prostředí Test Drive. Zákazník se otevře toto a můžete projít přesně co chcete udělat v rámci jejich testovací verze. Je důležité, že tento obsah je snadno srozumitelný a postupujte podle! (Musí být soubor PDF)

**Test jednotky – ukázkové Video -** *doporučená* podobně jako v uživatelské příručce, je nejlepší zahrnout videokurz prostředí Test Drive. Zákazník bude sledovat tuto předem nebo během jejich testovací jízdu a můžete projít přesně co chcete udělat v rámci jejich testovací verze. Je důležité, že tento obsah je snadno srozumitelný a postupujte podle!

- **Název** – název videa
- **Odkaz** – musí být vložený adresa URL z trubky nebo video. Příklad toho, jak získat adresu url vložené je nižší než:
- **Miniatura** – musí být vysoce kvalitní image pixelů (533 x 324). Doporučujeme pořídit snímek některých součástí prostředí Test Drive tady.

Níže je, jak tato pole objeví pro vaše zákazníky během jejich testovací verze.

![Umístění Test Drive polí v nabídce Marketplace](./media/azure-resource-manager-test-drive/howtopub4.png)

### <a name="technical-configuration"></a>Technické konfigurace

Další části a vyplňte je, když nahrávání šablony Resource Manageru jednotka testu a konkrétně jak definovat vaše testovací verze instance práce.

![](./media/azure-resource-manager-test-drive/howtopub5.png)

**Instance –** *vyžaduje* to je, kde nakonfigurujte počet instancí, které chcete, v jaké oblasti a jak rychle vaši zákazníci mohou získat testovací verze.

- **Instance** – The vyberte oblasti je, kde vyberete nasazená šablony Resource Manageru jednotky testů v. Doporučujeme vybrat pouze jednu oblast, kde je nejvíce vaši zákazníci budou umístěné v.
- **Horké** – instance číslo jednotky testů, které už jsou nasazené a čeká se na přístup za vybrané oblasti. Zákazníci mají okamžitě přístup k tento Test jednotky a nemusíte čekat pro nasazení. Výměnou za to je, že tyto instance nepřetržitý provoz na vaše předplatné Azure, takže bude mít za následek větší dostupnost nákladů. Důrazně doporučujeme mít **aspoň jeden aktivní instance**, jako většina vašich zákazníků nechcete čekat na dokončení úplné nasazení a je odkládací využití ze strany zákazníků.
- **Horké** – číslo jednotky testů v jedné oblasti, které jsou nasazené instance a potom virtuální počítač zastavit a ukládají ve službě Azure storage. Doba čekání teplé instancí je pomalejší než horká instancí, ale je také levnější provozuschopnost náklady na úložiště.
- **Studená** – číslo jednotky testů instancí na oblasti, případně je možné nasadit. Studená instance vyžadují celý Test Drive Resource Manageru šablony kvůli tomu provádět nasazení v době zákazník požaduje Test Drive, proto je pomalejší než horká nebo teplé instancí. Kompromis je, že máte jenom platí po dobu trvání na testovací verzi.

V tuto chvíli vypočítá celkový počet možných souběžných testovacími verzemi chcete zpřístupnit, a ověřte, že maximální kvóty pro vaše předplatné může zpracovávat souběžných šířka:

**(Počet vybrané oblasti x aktivní instance) + (číslo z oblasti dílčích x teplé instance) + (číslo z oblasti dílčích x studenou instance)**

**Jednotka doba trvání testu (hodiny) -** *vyžaduje* dobu jak dlouho Test Drive zůstane aktivní, v \# hodin. Po skončení tohoto období se automaticky ukončí na testovací verzi.

**Test Drive Resource Manageru šablony -** *vyžaduje* nahrát šablonu Resource Manageru. Toto je soubor, který jste vytvořili v předchozí části výše. Název souboru hlavní šablony: "hlavní template.json" a ujistěte se, že šablony Resource Manageru obsahuje výstupní parametry klíče proměnné, které jsou potřeba. (Musí být soubor typu ZIP)

**Přístup k informacím o -** *vyžaduje* po zákazník dostane jejich Test Drive, se jim prezentují přístup k informacím. Tyto pokyny jsou určené ke sdílení užitečné výstupní parametry z testovacího správce prostředků disku šablony. Chcete-li zahrnout výstupních parametrů, použijte dvojitých složených závorkách (například **{{outputname}}**), a že bude správně vložena do umístění. (Formátování řetězce ve formátu HTML je zde doporučeného k vykreslení v části front end).

### <a name="test-drive-deployment-subscription-details"></a>Podrobnosti předplatného nasazení testu jednotky

Závěrečné sekci o vyplnění je testovací verze automatické nasazení propojením vaše předplatné Azure a Azure Active Directory (AD).

![Podrobnosti testovací verze nasazení předplatného](./media/azure-resource-manager-test-drive/subdetails1.png)

**ID předplatného Azure -** *vyžaduje* tím udělíte přístup ke službám Azure a webu Azure portal. Předplatné je, když se použije v hlášení využití prostředků a služby se účtují. Pokud již nemáte **samostatné** předplatné Azure pro Test jednotek, pokračujte a provádějte jeden. ID předplatného Azure můžete najít tak, že přihlášení k webu Azure portal a navigace k předplatným v levé nabídce. (Příklad: "a83645ac-1234-5ab6-6789-1h234g764ghty")

![Předplatná Azure](./media/azure-resource-manager-test-drive/subdetails2.png)

**ID Tenanta Azure AD –** *vyžaduje* máte již k dispozici ID Tenanta najdete ho pod ve vlastnostech -\> ID adresáře.

![Vlastnosti služby Azure Active Directory](./media/azure-resource-manager-test-drive/subdetails3.png)

V opačném případě vytvořte nového Tenanta v Azure Active Directory.

![Přehled služby Azure Active Directory klienty](./media/azure-resource-manager-test-drive/subdetails4.png)

![Zadejte organizaci, domény a země pro tenanta Azure AD](./media/azure-resource-manager-test-drive/subdetails5.png)

![Potvrďte výběr](./media/azure-resource-manager-test-drive/subdetails6.png)

**ID aplikace Azure AD –** *vyžaduje* dalším krokem je vytvoření a registrace nové aplikace. Tuto aplikaci budeme používat k provádění operací ve vaší instanci testovací verze.

1. Přejděte do adresáře s nově vytvořený nebo již existující adresář a Azure Active directory, vyberte v podokně filtru.
2. Najděte "Registrace aplikací" a klikněte na "Add"
3. Zadejte název aplikace.
4. Vyberte typ jako "webové aplikace a rozhraní API"
5. Zadejte libovolnou hodnotu v přihlašovací adresa URL, vyhráli jsme\'t používá toto pole.
6. Klikněte na vytvořit.
7. Po vytvoření aplikace, přejděte do vlastností -\> nastavení aplikace jako víceklientské kliknu na Uložit.

Klikněte na Uložit. Posledním krokem je zkopírovat ID aplikace pro tuto registrované aplikaci a vložte ho do pole testovací verze.

![Podrobnosti o ID aplikace Azure AD](./media/azure-resource-manager-test-drive/subdetails7.png)

Zadané aplikace se používá k nasazení do předplatného, potřebujeme pro tuto aplikaci přidat jako přispěvatele daného předplatného. Tyto pokyny jsou jako níže:

1. Přejděte do okna odběry a vyberte odpovídající předplatné, který používáte pro pouze na testovací verzi.
1. Klikněte na tlačítko **řízení přístupu (IAM)**.
1. Klikněte na tlačítko **přiřazení rolí** kartu.  ![Přidat nový objekt řízení přístupu](./media/azure-resource-manager-test-drive/SetupSub7_1.jpg)
1. Klikněte na tlačítko **přidat přiřazení role**.
1. Nastavit jako roli **Přispěvatel**.
1. Zadejte název aplikace Azure AD a vyberte aplikaci, pokud chcete přiřadit roli.
    ![Přidat oprávnění](./media/azure-resource-manager-test-drive/SetupSub7_2.jpg)
1. Klikněte na **Uložit**.

**Klíč aplikace Azure AD –** *vyžaduje* poslední pole má generovat ověřovací klíč. V části klíče přidejte popis klíče, nastavte dobu trvání nikdy nevyprší, pak vyberte možnost uložit. Je **důležité** abyste se vyhnuli nutnosti vypršela klíčů, což naruší vaše testovací verze v produkčním prostředí. Zkopírujte tuto hodnotu a vložte ho do vašeho povinné pole testovací verze.

![Obsahuje seznam klíčů pro aplikaci Azure AD](./media/azure-resource-manager-test-drive/subdetails8.png)

<a name="next-steps"></a>Další postup
----------

Teď, když máte všechna vaše testovací verze pole doplnit, projděte si a **znovu publikovat** vaší nabídky. Jakmile se vaše testovací verze uplynutí certifikace, byste se měli zúčastnit rozsáhlé testování prostředí pro zákazníky v **ve verzi preview** o vaší nabídce. Spusťte si testovací jízdu v uživatelském rozhraní a otevřete vašeho předplatného Azure v portálu Azure portal a ověřte, že vaše testovací verze se plně nasazují správně.

![portál Azure](./media/azure-resource-manager-test-drive/subdetails9.png)

Je důležité si uvědomit, jako jsou zřízené pro vaše zákazníky, tak službu Test Drive automaticky vyčistí tyto skupiny prostředků po dokončení se zákazník není odstranit všechny instance testovací verze.

Jakmile budete s klidem s vaší nabídkou ve verzi Preview, nyní je čas **vysílat živě**! Po nabídky byl publikován do dvojitých vrácení celé prostředí koncového je posledním kontrolním procesu od Microsoftu. Pokud z nějakého důvodu získá zamítl nabídky, pošleme na technický kontakt pro vaši nabídku s vysvětlením, co bude potřebovat dojít k opravě oznámení.

Pokud máte další otázky, hledají Poradce při potížích nebo chtít, aby vaše testovací verze úspěšnější, přejděte prosím na [nejčastější dotazy, řešení potíží a osvědčené postupy](./marketing-and-best-practices.md).
