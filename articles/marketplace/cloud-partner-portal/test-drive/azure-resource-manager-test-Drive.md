---
title: Azure Resource Manager testovacích jednotek | Azure Marketplace
description: Sestavení testovací jednotky Marketplace pomocí Azure Resource Manager
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: dsindona
ms.openlocfilehash: 6125aa010d8676518b84f866343b01f95246160f
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "80275930"
---
# <a name="azure-resource-manager-test-drive"></a>Testovací verze Azure Resource Manageru

Tento článek je určen vydavatelům, kteří mají svou nabídku na Azure Marketplace nebo kteří jsou na AppSource, ale chtějí sestavit testovací jednotku pouze s prostředky Azure.

Šablona Azure Resource Manager (Správce prostředků) je kódovaný kontejner prostředků Azure, který navrhujete, aby nejlépe představoval vaše řešení. Pokud si nejste obeznámeni s tím, co je šablona Správce prostředků, přečtěte si článek [principy správce prostředků šablon](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) a [vytváření správce prostředků šablon](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authoring-templates) , abyste se ujistili, jak vytvářet a testovat vlastní šablony.

V jakém testovacím disku se převezme poskytnutá šablona Správce prostředků a provede nasazení všech prostředků vyžadovaných z této Správce prostředků šablony do skupiny prostředků.

Pokud se rozhodnete vytvořit Azure Resource Manager testovací jednotku, požadavky vám umožní:

- Sestavte, testujte a nahrajte svou testovací jednotku Správce prostředků šablonu.
- Nakonfigurujte všechna požadovaná metadata a nastavení, aby se aktivovala testovací jednotka.
- Znovu publikujte nabídku se zapnutou testovací jednotkou.

## <a name="how-to-build-an-azure-resource-manager-test-drive"></a>Postup sestavení testovací jednotky Azure Resource Manager

Tady je postup pro vytvoření Azure Resource Manager testovacích jednotek:

1. Navrhněte, co mají vaši zákazníci dělat v diagramu toku.
1. Definujte, co byste chtěli, aby si vaši zákazníci mohli sestavit.
1. Na základě výše uvedených definic rozhodněte, které součásti a prostředky jsou nutné pro zákazníky k tomu, aby mohli provádět tyto zkušenosti: například instance D365 nebo web s databází.
1. Sestavte návrh místně a otestujte prostředí.
1. Zabalit prostředí v nasazení šablony ARM a odtud:
    1. Určete, jaké části prostředků jsou vstupní parametry;
    1. Jaké proměnné jsou;
    1. Jaké jsou výstupy daného zákazníka.
1. Publikujte, testujte a jděte do provozu.

Nejdůležitější součástí vytváření Azure Resource Manager testovacích jednotek je definování scénářů, ve kterých mají vaši zákazníci zkušenosti. Jste produkt brány firewall a chcete si vyzkoušet, jak dobře zpracovávat útoky s injektážemi skriptu? Jste produkt úložiště a chcete si vyzkoušet, jak rychlé a snadné řešení komprimuje soubory?

Nezapomeňte strávit dostatečný čas hodnocením, jaké jsou nejlepší způsoby, jak se má váš produkt zobrazit. Konkrétně pro všechny požadované prostředky, které byste potřebovali, protože balíček Správce prostředků šablonu dostatečně jednodušší.

Aby bylo možné pokračovat v našem příkladu brány firewall, může to být tím, že budete potřebovat veřejnou IP adresu URL pro vaši službu a jinou veřejnou IP adresu URL pro web, který brána firewall chrání. Každá IP adresa je nasazená na virtuálním počítači a připojená společně se skupinou zabezpečení sítě + síťovým rozhraním.

Po navržení požadovaného balíčku prostředků teď dovedeme k vytváření a sestavování testovacích jednotek Správce prostředků šablony.

## <a name="writing-test-drive-resource-manager-templates"></a>Zápis šablon testovacích jednotek Správce prostředků

Test Drive spouští nasazení v plně automatizovaném režimu a z toho důvodu mají šablony testovacích jednotek některá omezení popsaná níže.

### <a name="parameters"></a>Parametry

Většina šablon má sadu parametrů. Parametry definují názvy prostředků, velikosti prostředků (například typy účtů úložiště nebo velikosti virtuálních počítačů), uživatelská jména a hesla, názvy DNS atd. Když nasadíte řešení pomocí Azure Portal, můžete ručně naplnit všechny tyto parametry, vybrat dostupné názvy DNS nebo názvy účtů úložiště a tak dále.

![Seznam parametrů v Azure Resource Manager](./media/azure-resource-manager-test-drive/param1.png)

Testovací jednotka ale funguje v plně automatickém režimu bez lidské interakce, takže podporuje jenom omezenou sadu kategorií parametrů. Pokud parametr v šabloně Test Drive Správce prostředků nepatří do jedné z podporovaných kategorií, je nutné **nahradit tento parametr proměnnou nebo konstantní hodnotou.**

Pro parametry můžete použít libovolný platný název. Test Drive rozpoznává kategorii parametru pomocí hodnoty typu metadata. Je **nutné zadat metadata-Type pro každý parametr šablony**, jinak vaše šablona neprojde ověřením:

```json
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
```

Je také důležité si uvědomit, že **všechny parametry jsou volitelné**, takže pokud\'nechcete použít žádné, nebudete\'potřebovat.

### <a name="accepted-parameter-metadata-types"></a>Přijaté typy metadat parametrů

| Typ metadat   | Typ parametru  | Popis     | Ukázková hodnota    |
|---|---|---|---|
| **identifikátor**     | řetězec          | Základní identifikátor URI balíčku pro nasazení| https:\//\<.\. \>. blob.Core.Windows.NET/\<\..\> |
| **jmen**    | řetězec          | Nové náhodné uživatelské jméno.| admin68876      |
| **heslo**    | zabezpečený řetězec    | Nové náhodné heslo | LP! ACS\^2kH     |
| **ID relace**   | řetězec          | Jedinečný identifikátor relace testovacích jednotek (GUID)    | b8c8693e-5673-449c-badd-257a405a6dee |

#### <a name="baseuri"></a>identifikátor

Test Drive Inicializuje tento parametr se **základním identifikátorem URI** balíčku pro nasazení, takže tento parametr můžete použít k vytvoření identifikátoru URI všech souborů zahrnutých do balíčku.

```json
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
```

V rámci šablony můžete pomocí tohoto parametru vytvořit identifikátor URI libovolného souboru z balíčku pro nasazení testovacích jednotek. Následující příklad ukazuje, jak vytvořit identifikátor URI propojené šablony:

```json
"templateLink": {
  "uri": "[concat(parameters('baseuri'),'templates/solution.json')]",
  "contentVersion": "1.0.0.0"
}
```

#### <a name="username"></a>uživatelské jméno

Test Drive Inicializuje tento parametr pomocí nového náhodného uživatelského jména:

```json
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
```

Ukázková hodnota:

    admin68876

Pro vaše řešení můžete použít buď náhodná, nebo trvalá uživatelská jména.

#### <a name="password"></a>heslo

Test Drive Inicializuje tento parametr pomocí nového náhodného hesla:

```json
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
```

Ukázková hodnota:

    Lp!ACS^2kh

Pro vaše řešení můžete použít náhodná nebo trvalá hesla.

#### <a name="session-id"></a>ID relace

Otestujte tento parametr pomocí jedinečného identifikátoru GUID, který představuje ID relace testovací jednotky:

```json
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
```

Ukázková hodnota:

    b8c8693e-5673-449c-badd-257a405a6dee

Tento parametr můžete použít k jednoznačné identifikaci relace testovacích jednotek, pokud je to nezbytné.

### <a name="unique-names"></a>Jedinečné názvy

Některé prostředky Azure, jako jsou účty úložiště nebo názvy DNS, vyžadují globálně jedinečné názvy.

To znamená, že pokaždé, když testovací jednotka nasadí šablonu Správce prostředků, vytvoří **novou skupinu prostředků s jedinečným názvem** pro všechny její\' prostředky. Proto je nutné použít funkci [uniquestring](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-functions#uniquestring) zřetězenou s názvy proměnných v ID skupiny prostředků pro generování náhodných jedinečných hodnot:

```json
"variables": {
  ...
  "domainNameLabel": "[concat('contosovm',uniquestring(resourceGroup().id))]",
  "storageAccountName": "[concat('contosodisk',uniquestring(resourceGroup().id))]",
  ...
}
```

Ujistěte se, že jste zřetězené řetězce parametrů nebo proměnných\'(\'contosovm) s jedinečným výstupem\'řetězce (resourceName (). ID\'), protože to zaručuje jedinečnost a spolehlivost každé proměnné.

Například většina názvů prostředků nemůže začínat číslicí, ale funkce Unique String může vracet řetězec, který začíná číslicí. Takže pokud použijete nezpracovaný jedinečný výstup řetězce, vaše nasazení se nezdaří. 

Další informace o pravidlech a omezeních názvů prostředků najdete v [tomto článku](/azure/cloud-adoption-framework/ready/azure-best-practices/naming-and-tagging).

### <a name="deployment-location"></a>Umístění nasazení

K dispozici je možnost testovat jednotku v různých oblastech Azure. Cílem je umožnit uživateli vybrat nejbližší oblast, která bude poskytovat touchdown uživatelské prostředí.

Když testovací jednotka vytvoří instanci testovacího prostředí, vždy vytvoří skupinu prostředků v oblasti, kterou vybere uživatel, a pak v tomto kontextu skupiny spustí šablonu nasazení. Proto by šablona měla vybrat umístění nasazení ze skupiny prostředků:

```json
"variables": {
  ...
  "location": "[resourceGroup().location]",
  ...
}
```

A pak použijte toto umístění pro všechny prostředky konkrétní instance testovacího prostředí:

```json
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
```

Musíte zajistit, aby vaše předplatné mělo povoleno nasazení všech prostředků, které chcete nasadit, do všech oblastí, které vyberete. Také je potřeba zajistit, aby image virtuálních počítačů byly dostupné ve všech oblastech, které chcete povolit, jinak nebude šablona nasazení fungovat pro některé oblasti.

### <a name="outputs"></a>Výstupy

Normálně se šablonami Správce prostředků můžete nasadit bez nutnosti vytvářet výstup. Je to proto, že znáte všechny hodnoty, které použijete k naplnění parametrů šablony, a můžete vždy ručně zkontrolovat vlastnosti libovolného prostředku.

U testovacích jednotek Správce prostředků šablon ale\'je důležité vrátit se k testovacím jednotkám všechny informace, které jsou nutné k získání přístupu k testovacímu prostředí (identifikátory URI webů, názvy hostitelů virtuálních počítačů, uživatelská jména a hesla). Ujistěte se, že jsou všechny vaše výstupní názvy čitelné, protože se tyto proměnné prezentují zákazníkovi.

Neexistují žádná omezení související s výstupem šablony. Stačí si pamatovat, testovací jednotka převede všechny výstupní hodnoty na **řetězce**, takže pokud odešlete objekt do výstupu, uživatel uvidí řetězec JSON.

Příklad:

```json
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
```

### <a name="subscription-limits"></a>Omezení předplatného

Další věcí, které byste měli vzít v úvahu, je předplatné a omezení služeb. Pokud například chcete nasadit až deset virtuálních počítačů se 4 jádry, musíte se ujistit, že předplatné, které používáte pro testovací prostředí, vám umožní používat 40 jader.

Další informace o omezeních předplatného a služeb Azure najdete v [tomto článku](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits). Když lze současně provést více testovacích jednotek, ověřte, že vaše předplatné může zpracovávat \# jader vynásobené celkovým počtem souběžných testovacích jednotek, které lze provést.

### <a name="what-to-upload"></a>Co nahrát

Testovací jednotka Správce prostředků šablona se nahraje jako soubor zip, který může obsahovat různé artefakty nasazení, ale musí mít jeden soubor s názvem **Main-Template. JSON**. Tento soubor je Azure Resource Manager šablonou nasazení a testovací jednotka ho používá k vytvoření instance testovacího prostředí.

Pokud máte další prostředky za tento soubor, můžete na něj odkazovat jako na externí prostředek uvnitř šablony, nebo můžete prostředek zahrnout do souboru ZIP.

Během certifikace publikování testovací jednotka rozbalí balíček pro nasazení a vloží jeho obsah do kontejneru objektů BLOB interního testovacího disku. Struktura kontejneru odráží strukturu balíčku pro nasazení:

| Package. zip                       | Kontejner objektů BLOB testovací jednotky         |
|---|---|
| hlavní šablona. JSON                | \//\<https:\... \>. blob.Core.Windows.NET/\<\... \>/Main-Template.JSON  |
| šablony/řešení. JSON           | \//\<https:\... \>. blob.Core.Windows.NET/\<\... \>/Templates/Solution.JSON |
| skripty/zahřívání. ps1                | \//\<https:\... \>. blob.Core.Windows.NET/\<\... \>/Scripts/warmup.ps1  |


Voláme identifikátor URI tohoto základního identifikátoru URI kontejneru objektů BLOB. Každá revize vašeho testovacího prostředí má vlastní kontejner objektů blob, a proto každá revize vašeho testovacího prostředí má svůj vlastní základní identifikátor URI. Testovací jednotka může předat základní identifikátor URI vašeho balíčku pro nasazení unzip do šablony prostřednictvím parametrů šablony.

## <a name="transforming-template-examples-for-test-drive"></a>Transformují se příklady šablon pro Test Drive.

Proces od zapnutí architektury prostředků do testovací jednotky Správce prostředků šablona může být těžké. Abychom vám usnadnili tento proces, jsme\'udělali příklady, jak nejlépe [transformovat aktuální šablony nasazení](./transforming-examples-for-test-drive.md).

## <a name="how-to-publish-a-test-drive"></a>Postup publikování testovacího disku

Teď, když máte vytvořenou testovací jednotku, Tato část vás provede všemi poli potřebnými k úspěšnému publikování testovacích jednotek.

![Povolení testovací jednotky v uživatelském rozhraní](./media/azure-resource-manager-test-drive/howtopub1.png)

Prvním a nejnejdůležitějším polem je přepínat, jestli chcete, aby se pro vaši nabídku povolila testovací jednotka, nebo ne. Když vyberete **Ano,** zobrazí se zbytek formuláře se všemi požadovanými poli, abyste mohli vyplnit. Když vyberete **Ne,** formulář se deaktivuje a pokud znovu publikujete s neaktivovanou testovací jednotkou, bude testovací jednotka z produkčního prostředí odebrána.

Poznámka: Pokud jsou k dispozici nějaké jednotky testů, budou tyto testovací jednotky nadále běžet až do vypršení platnosti jejich relace.

### <a name="details"></a>Podrobnosti

Další část, která se má vyplnit, jsou podrobnosti o nabídce testovacích jednotek.

![Podrobné informace o testovacích jednotkách](./media/azure-resource-manager-test-drive/howtopub2.png)

**Popis –** *požaduje* se, když zapíšete hlavní popis toho, co je na testovací jednotce. Zde se zobrazí zákazník, který si přečte scénáře, na které se vaše testovací jednotka vztahuje na váš produkt. 

**Ruční – vyžadováno uživatelem –** *Required* Toto je podrobný návod k vašemu prostředí Test Drive. Zákazník ho otevře a bude moct procházet přesně to, co chcete dělat v rámci své testovací jednotky. Je důležité, abyste tento obsah snadno pochopili a následovali! (Musí se jednat o soubor. PDF)

**Ukázkové video na testovacím disku –** *Doporučené* pro použití jako ruční příručka pro uživatele, je nejlepší zahrnout video kurz prostředí Test Drive. Zákazník si tento postup předá nebo během testovacích jednotek dokončí a může procházet přesně to, co chcete v rámci své zkušební jednotky provádět. Je důležité, abyste tento obsah snadno pochopili a následovali!

- **Název** – název videa
- **Odkaz** – musí se jednat o VLOŽENOU adresu URL z vaší trubice nebo videa. Příklad toho, jak získat vloženou adresu URL, je níže:
- **Miniatura** – musí to být obrázek s vysokou kvalitou (533x324) pixelů. Doporučujeme udělat snímek obrazovky některé z možností testování testovacích jednotek.

Níže je uveden způsob, jakým se tato pole zobrazí pro zákazníka během používání testovacích jednotek.

![Umístění polí testovacích jednotek v nabídce Marketplace](./media/azure-resource-manager-test-drive/howtopub4.png)

### <a name="technical-configuration"></a>Technická konfigurace

Další část, která se má vyplnit, je místo, kam nahrajete šablonu Test Drive Správce prostředků a definujte, jak konkrétně vaše instance testovacích jednotek fungují.

![](./media/azure-resource-manager-test-drive/howtopub5.png)

**Instance –** to je *nutné* v případě, že nakonfigurujete, kolik instancí chcete, v jaké oblasti a jak rychle můžou zákazníci získat testovací jednotku.

- **Instance** – výběrové oblasti je místo, kde můžete vybrat, kde je vaše testovací jednotka správce prostředků šablona nasazena. Doporučuje se jen vybrat jednu oblast, kde očekáváte, že se zákazníci nacházejí v.
- **Hot** -počet instancí testovacích jednotek, které jsou už nasazené a čekají na přístup pro vybranou oblast. Zákazníci můžou k těmto testovacím jednotkám okamžitě přistupovat, ale nemusíte čekat na nasazení. Kompromisem je, že tyto instance jsou vždycky spuštěné v předplatném Azure, takže budou mít větší náklady na dobu provozu. Důrazně doporučujeme mít **aspoň jednu aktivní instanci**, protože většina vašich zákazníků nechce čekat na dokončení kompletních nasazení, a proto je v zákaznickém využívání k dispozici rozevírací seznam.
- **Zahřívání** – počet instancí testovacích jednotek na oblast, které byly nasazeny, a potom byl virtuální počítač zastaven a uložen ve službě Azure Storage. Čekací doba pro teplé instance je pomalejší než v Hot instance, ale náklady na úložiště jsou také levnější.
- **Studená** řada instancí testovacích jednotek na oblast, která může být nasazena. Studené instance vyžadují celou Správce prostředků šablonu testovacích jednotek, aby procházela nasazením v okamžiku, kdy zákazník požaduje testovací verzi, takže je pomalejší než horká nebo Teplová instance. Kompromisy ale platí jenom za dobu trvání testovacích jednotek.

V tuto chvíli vypočítá celkový počet potenciálních souběžných testovacích jednotek, které budete mít k dispozici, a ověřte, že vaše omezení kvóty pro vaše předplatné může zvládnout tuto souběžnou částku:

**(Počet oblastí vybraných v × Hot instance) + (počet oblastí vybraných × teplé instance) + (počet oblastí vybraných x studených instancí)**

**Doba trvání testovacího prostředí (v hodinách) –** *požadovaná* doba, po kterou testovací jednotka zůstane aktivní, \# v hodinách. Testovací jednotka se po skončení tohoto časového období ukončí automaticky.

**Test Drive správce prostředků šablona –** zde můžete nahrát šablonu správce prostředků, která je *povinná* . Jedná se o soubor, který jste vytvořili v předchozí části výše. Pojmenujte hlavní soubor šablony: "Main-Template. JSON" a ujistěte se, že šablona Správce prostředků obsahuje výstupní parametry pro klíčové proměnné, které jsou potřeba. (Musí se jednat o soubor. zip)

**Přístup k informacím –** *povinné* , když zákazník získá testovací verzi, zobrazí se jim informace o přístupu. Tyto pokyny jsou určeny pro sdílení užitečných výstupních parametrů z testovacích jednotek Správce prostředků šabloně. Chcete-li zahrnout výstupní parametry, použijte dvojité složené závorky (například **{{outputName}}**) a budou vloženy do umístění správně. (Pro vykreslení v front-endu se doporučuje formátování řetězce HTML.)

### <a name="test-drive-deployment-subscription-details"></a>Podrobnosti o předplatném nasazení testovacích jednotek

Poslední část, která se má vyplnit, je umožnit automatické nasazení testovacích jednotek tím, že se připojíte k předplatnému Azure a Azure Active Directory (AD).

![Podrobnosti o předplatném nasazení testovacích jednotek](./media/azure-resource-manager-test-drive/subdetails1.png)

**ID předplatného Azure –** *vyžaduje* se k tomu přístup ke službám Azure a Azure Portal. Předplatné je místo, kde se oznamuje využití prostředků, a účtují se služby. Pokud ještě nemáte **samostatné** předplatné Azure pro testovací disky, pokračujte a udělejte si ho. ID předplatných Azure můžete najít tak, že se přihlásíte Azure Portal a v nabídce na levé straně přejdete k předplatným. (Příklad: "a83645ac-1234-5ab6-6789-1h234g764ghty")

![Předplatná Azure](./media/azure-resource-manager-test-drive/subdetails2.png)

**ID tenanta Azure AD –** *povinné* Pokud máte ID tenanta, které je už dostupné, najdete ho níže v části vlastnosti –\> ID adresáře.

![Vlastnosti Azure Active Directory](./media/azure-resource-manager-test-drive/subdetails3.png)

V opačném případě vytvořte nového tenanta v Azure Active Directory.

![Seznam tenantů Azure Active Directory](./media/azure-resource-manager-test-drive/subdetails4.png)

![Definujte organizaci, doménu a zemi/oblast pro tenanta Azure AD.](./media/azure-resource-manager-test-drive/subdetails5.png)

![Potvrdit výběr](./media/azure-resource-manager-test-drive/subdetails6.png)

**Aplikace Azure AD ID –** *vyžadováno* dalším krokem je vytvoření a registrace nové aplikace. Tuto aplikaci použijeme k provádění operací s instancí testovacích jednotek.

1. Přejděte do nově vytvořeného adresáře nebo už existujícího adresáře a v podokně filtru vyberte Azure Active Directory.
2. Vyhledejte "Registrace aplikací" a klikněte na Přidat.
3. Zadejte název aplikace.
4. Vyberte typ "webová aplikace/rozhraní API".
5. Zadejte libovolnou hodnotu v přihlašovací adrese URL,\'kterou toto pole používáme.
6. Klikněte na vytvořit.
7. Po vytvoření aplikace přejděte na vlastnosti –\> nastavte aplikaci jako více tenantů a klikněte na Uložit.

Klikněte na Uložit. Posledním krokem je vyvýšení ID aplikace pro tuto registrovanou aplikaci a její vložení do pole testovací jednotka.

![Podrobnosti o ID aplikace Azure AD](./media/azure-resource-manager-test-drive/subdetails7.png)

V případě, že aplikaci používáme k nasazení do předplatného, musíme aplikaci přidat jako přispěvatele v předplatném. Pokyny k těmto akcím jsou uvedené níže:

1. Přejděte do okna předplatná a vyberte příslušné předplatné, které používáte pouze pro testovací jednotku.
1. Klikněte na **Řízení přístupu (IAM)**.
1. Klikněte na kartu **přiřazení rolí** .  ![Přidat nový objekt zabezpečení Access Control](./media/azure-resource-manager-test-drive/SetupSub7_1.jpg)
1. Klikněte na **Přidat přiřazení role**.
1. Nastavte roli jako **Přispěvatel**.
1. Zadejte název aplikace Azure AD a vyberte aplikaci, pro kterou chcete roli přiřadit.
    ![Přidat oprávnění](./media/azure-resource-manager-test-drive/SetupSub7_2.jpg)
1. Klikněte na **Uložit**.

**Aplikace Azure AD klíč –** konečné pole *vyžaduje* , aby bylo vygenerován ověřovací klíč. V části klíče přidejte popis klíče, nastavte dobu trvání na bez vypršení platnosti a pak vyberte Uložit. Je **důležité** , abyste se vyhnuli vypršení platnosti klíče, což způsobí přerušení testovacích jednotek v produkčním prostředí. Zkopírujte tuto hodnotu a vložte ji do pole požadované testovací jednotky.

![Zobrazuje klíče pro aplikaci Azure AD.](./media/azure-resource-manager-test-drive/subdetails8.png)

## <a name="next-steps"></a>Další kroky

Teď, když máte vyplněná všechna pole testovacích jednotek, Projděte a **znovu publikujte** vaši nabídku. Po úspěšném dokončení testu byste si měli projít prostředí pro zákazníky ve **verzi Preview** vaší nabídky. Spusťte testovací jednotku v uživatelském rozhraní a pak otevřete své předplatné Azure v rámci Azure Portal a ověřte, jestli jsou vaše testovací jednotky správně nasazené.

![portál Azure](./media/azure-resource-manager-test-drive/subdetails9.png)

Je důležité si uvědomit, že neodstraňujete žádné instance testovacích jednotek, protože jsou zřízené pro vaše zákazníky, takže služba testovacích jednotek tyto skupiny prostředků po dokončení zákazníka automaticky vyčistí.

Jakmile budete mít v nabídce Preview pohodlí, teď je to čas do **Live**! Po publikování nabídky od Microsoftu probíhá finální proces kontroly, který vám umožní dvakrát zkontrolovat celý konečný zážitek. Pokud z nějakého důvodu se nabídka odmítne, pošleme oznámení technickému kontaktu pro vaši nabídku, která vysvětluje, co je potřeba k tomu, abychom mohli opravit.

Pokud máte více otázek, hledáte Rady pro řešení potíží nebo chcete, aby testovací verze byla více úspěšná, přejděte na [Nejčastější dotazy, řešení potíží & osvědčené postupy](./marketing-and-best-practices.md).
