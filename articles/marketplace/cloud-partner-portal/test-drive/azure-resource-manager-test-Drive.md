---
title: Testovací cesta Správce prostředků Azure | Azure Marketplace
description: Vytvoření testovací jednotky Marketplace pomocí Správce prostředků Azure
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: dsindona
ms.openlocfilehash: 6125aa010d8676518b84f866343b01f95246160f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80275930"
---
# <a name="azure-resource-manager-test-drive"></a>Testovací verze Azure Resource Manageru

Tento článek je pro vydavatele, kteří mají svou nabídku na Azure Marketplace nebo kteří jsou na AppSource, ale chtějí vytvořit svou testovací disku s jenom prostředky Azure.

Šablona Azure Resource Manager (Resource Manager) je kódovaný kontejner prostředků Azure, které navrhujete tak, aby co nejlépe reprezentovaly vaše řešení. Pokud nejste obeznámeni s tím, co je šablona Správce prostředků, přečtěte si informace o [porozumění šablonám Správce prostředků](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) a vytváření šablon Správce [prostředků,](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authoring-templates) abyste měli jistotu, že víte, jak vytvářet a testovat vlastní šablony.

Testovací jednotka provádí, že přebírá zajišťovnou šablonu Správce prostředků a provádí nasazení všech prostředků požadovaných z této šablony Správce prostředků do skupiny prostředků.

Pokud se rozhodnete vytvořit testovací jednotku Azure Resource Manager, požadavky jsou pro vás:

- Vytvořte, otestujte a nahrajte šablonu Správce prostředků testovací jednotky.
- Nakonfigurujte všechna požadovaná metadata a nastavení tak, aby byla testovací verze povolena.
- Znovu publikujte svou nabídku s povolenou testovací jízdou.

## <a name="how-to-build-an-azure-resource-manager-test-drive"></a>Jak vytvořit testovací jednotku Azure Resource Manager

Tady je proces vytváření testovací jednotky Azure Resource Manager:

1. Navrhněte, co mají vaši zákazníci dělat v vývojovém diagramu.
1. Definujte, jaké prostředí mají zákazníci vytvářet.
1. Na základě výše uvedených definic se rozhodněte, jaké části a zdroje jsou potřebné k tomu, aby zákazníci dosáhli takového prostředí: například instance D365 nebo web s databází.
1. Vytvořte návrh místně a otestujte prostředí.
1. Balíček zkušenosti v nasazení šablony ARM a odtud:
    1. Definujte, které části prostředků jsou vstupní parametry;
    1. Jaké proměnné jsou;
    1. Jaké výstupy jsou dány zkušenosti zákazníků.
1. Publikujte, otestujte a přejděte do provozu.

Nejdůležitější část o vytváření Azure Resource Manager Test Drive je definovat, jaké scénáře, které chcete, aby vaši zákazníci nazkušenosti. Jste firewall produkt a chcete demo, jak dobře zvládnout skript injekce útoky? Jste úložný produkt a chcete demo, jak rychle a snadno vaše řešení komprimuje soubory?

Ujistěte se, že trávíte dostatečné množství času hodnocením toho, jaké jsou nejlepší způsoby, jak předvést svůj produkt. Konkrétně kolem všech požadovaných prostředků, které byste potřebovali, protože balení šablony Resource Manager je dostatečně jednodušší.

Chcete-li pokračovat v našem příkladu brány firewall, architektura může být, že potřebujete veřejnou ADRESU IP pro vaši službu a další veřejnou adresu IP adresu URL pro web, který chrání brána firewall. Každá IP adresa je nasazená ve virtuálním počítači a propojena se skupinou zabezpečení sítě + síťovým rozhraním.

Jakmile jste navrhli požadovaný balíček prostředků, nyní přichází psaní a vytváření šablony Test Drive Resource Manager.

## <a name="writing-test-drive-resource-manager-templates"></a>Psaní šablon Správce prostředků testovací jednotky

Test Drive spustí nasazení v plně automatizovaném režimu a z tohoto důvodu mají šablony testovací jednotky některá omezení popsaná níže.

### <a name="parameters"></a>Parametry

Většina šablon má sadu parametrů. Parametry definují názvy prostředků, velikosti prostředků (například typy účtů úložiště nebo velikosti virtuálních počítačů), uživatelská jména a hesla, názvy DNS a tak dále. Když nasadíte řešení pomocí portálu Azure Portal, můžete ručně naplnit všechny tyto parametry, vybrat dostupné názvy DNS nebo názvy účtů úložiště a tak dále.

![Seznam parametrů ve Správci prostředků Azure](./media/azure-resource-manager-test-drive/param1.png)

Test Drive však funguje v plně automatickém režimu bez lidské interakce, takže podporuje pouze omezenou sadu kategorií parametrů. Pokud parametr v šabloně Správce prostředků testovací jednotky nespadá do jedné z podporovaných kategorií, je nutné **tento parametr nahradit proměnnou nebo konstantní hodnotou.**

Můžete použít libovolný platný název pro vaše parametry, Test Drive rozpoznává kategorii parametrů pomocí hodnoty typu metadat. Pro **každý parametr šablony je nutné zadat typ metadat**, jinak šablona neprojde ověřením:

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

Je také důležité si uvědomit, že **všechny parametry jsou volitelné**, takže pokud\'nechcete používat žádné, nemusíte.\'

### <a name="accepted-parameter-metadata-types"></a>Typy metadat přijatých parametrů

| Typ metadat   | Typ parametru  | Popis     | Vzorová hodnota    |
|---|---|---|---|
| **Baseuri**     | řetězec          | Základní identifikátor URI balíčku nasazení| \//\<https:\.. \>.blob.core.windows.net/\<\..\> |
| **Username**    | řetězec          | Nové náhodné uživatelské jméno.| admin68876      |
| **heslo**    | zabezpečený řetězec    | Nové náhodné heslo | Lp!ACS\^2kh     |
| **id relace**   | řetězec          | Jedinečné ID relace testovací jednotky (GUID)    | b8c8693e-5673-449c-badd-257a405a6dee |

#### <a name="baseuri"></a>Baseuri

Test Drive inicializuje tento parametr s **Base Uri** balíčku nasazení, takže můžete použít tento parametr k vytvoření Uri libovolného souboru zahrnutého do balíčku.

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

Uvnitř šablony můžete tento parametr použít k vytvoření Uri libovolného souboru z balíčku nasazení testovací jednotky. Následující příklad ukazuje, jak vytvořit uri propojené šablony:

```json
"templateLink": {
  "uri": "[concat(parameters('baseuri'),'templates/solution.json')]",
  "contentVersion": "1.0.0.0"
}
```

#### <a name="username"></a>uživatelské jméno

Test Drive inicializuje tento parametr s novým náhodným uživatelským jménem:

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

Hodnota vzorku:

    admin68876

Pro vaše řešení můžete použít náhodná nebo konstantní uživatelská jména.

#### <a name="password"></a>heslo

Test Drive inicializuje tento parametr novým náhodným heslem:

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

Hodnota vzorku:

    Lp!ACS^2kh

Pro vaše řešení můžete použít náhodná nebo konstantní hesla.

#### <a name="session-id"></a>ID relace

Test Drive inicializovat tento parametr s jedinečným identifikátorem GUID představující md relace testovací jednotky:

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

Hodnota vzorku:

    b8c8693e-5673-449c-badd-257a405a6dee

Tento parametr můžete použít k jednoznačné identifikaci relace testová jednotka, pokud je to nutné.

### <a name="unique-names"></a>Jedinečné názvy

Některé prostředky Azure, jako jsou účty úložiště nebo názvy DNS, vyžadují globálně jedinečné názvy.

To znamená, že pokaždé, když testdrive nasadí šablonu Správce prostředků,\' vytvoří **novou skupinu prostředků s jedinečným názvem** pro všechny své prostředky. Proto je nutné použít [funkci uniquestring](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-functions#uniquestring) zřetězenou s názvy proměnných na ID skupiny prostředků ke generování náhodných jedinečných hodnot:

```json
"variables": {
  ...
  "domainNameLabel": "[concat('contosovm',uniquestring(resourceGroup().id))]",
  "storageAccountName": "[concat('contosodisk',uniquestring(resourceGroup().id))]",
  ...
}
```

Ujistěte se, že zřetězíte řetězce\'parametrů/proměnných\'(contosovm) s jedinečným\'řetězcovým výstupem (resourceGroup().id\'), protože to zaručuje jedinečnost a spolehlivost každé proměnné.

Většina názvů prostředků například nemůže začínat číslicí, ale jedinečná funkce řetězce může vrátit řetězec, který začíná číslicí. Pokud tedy použijete nezpracovaný výstup jedinečného řetězce, vaše nasazení se nezdaří. 

Další informace o pravidlech a omezeních pro pojmenování prostředků naleznete v [tomto článku](/azure/cloud-adoption-framework/ready/azure-best-practices/naming-and-tagging).

### <a name="deployment-location"></a>Umístění nasazení

Test Drive můžete zpřístupnit v různých oblastech Azure. Cílem je umožnit uživateli vybrat nejbližší oblast, poskytnout s beast uživatelské zkušenosti.

Když Test Drive vytvoří instanci testovacího prostředí, vždy vytvoří skupinu prostředků v oblasti vybrané uživatelem a potom provede šablonu nasazení v tomto kontextu skupiny. Šablona by tedy měla vybrat umístění nasazení ze skupiny prostředků:

```json
"variables": {
  ...
  "location": "[resourceGroup().location]",
  ...
}
```

A pak použijte toto umístění pro každý prostředek pro konkrétní instanci Lab:

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

Musíte se ujistit, že vaše předplatné je povoleno nasadit všechny prostředky, které chcete nasadit v každé oblasti, kterou vybíráte. Také je třeba se ujistit, že vaše image virtuálních strojů jsou k dispozici ve všech oblastech, které se chystáte povolit, jinak vaše šablona nasazení nebude fungovat pro některé oblasti.

### <a name="outputs"></a>Výstupy

Normálně se šablonami Správce prostředků můžete nasadit bez vytváření jakéhokoli výstupu. Důvodem je, že znáte všechny hodnoty, které používáte k naplnění parametrů šablony a můžete vždy ručně zkontrolovat vlastnosti libovolného prostředku.

Pro šablony Správce prostředků testovací\'jednotky je však důležité vrátit se na test, aby se všechny informace, které jsou nutné k získání přístupu k testovacímu prostředí (identifikátory URI webu, názvy hostitelů virtuálních počítačů, uživatelská jména a hesla). Ujistěte se, že všechny vaše výstupní názvy jsou čitelné, protože tyto proměnné jsou prezentovány zákazníkovi.

Neexistují žádná omezení týkající se výstupů šablony. Jen nezapomeňte, Test Drive převádí všechny výstupní hodnoty na **řetězce**, takže pokud odešlete objekt do výstupu, uživatel uvidí řetězec JSON.

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

### <a name="subscription-limits"></a>Limity předplatného

Ještě jedna věc, kterou byste měli vzít v úvahu, je předplatné a limity služeb. Například pokud chcete nasadit až deset 4jádrových virtuálních počítačů, musíte se ujistit, že předplatné, které používáte pro vaši laboratoř, umožňuje používat 40 jader.

Další informace o limitech předplatného Azure a služeb najdete v [tomto článku](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits). Jako více testovacích jednotek lze přijmout současně, ověřte, že vaše předplatné může zpracovat \# jader vynásobené celkový počet souběžných testovacích jednotek, které lze přijmout.

### <a name="what-to-upload"></a>Co nahrát

Šablona Správce prostředků testovací jednotky je odeslána jako soubor ZIP, který může obsahovat různé artefakty nasazení, ale musí mít jeden soubor s názvem **main-template.json**. Tento soubor je šablona nasazení Azure Resource Manager a Test Drive používá k vytvoření instance Lab.

Pokud máte další prostředky nad rámec tohoto souboru, můžete odkazovat jako externí prostředek uvnitř šablony, nebo můžete zahrnout prostředek do souboru zip.

Během certifikace publikování Test Drive rozbalí balíček nasazení a vloží jeho obsah do interního kontejneru objektů blob testovací jednotky. Struktura kontejneru odráží strukturu balíčku nasazení:

| package.zip                       | Kontejner objektů blob testovací jednotky         |
|---|---|
| main-template.json                | \//\<https:\... \>.blob.core.windows.net/\<\... \>/main-template.json  |
| šablony/řešení.json           | \//\<https:\... \>.blob.core.windows.net/\<\... \>/templates/solution.json |
| skripty/warmup.ps1                | \//\<https:\... \>.blob.core.windows.net/\<\... \>/skripty/warmup.ps1  |


Nazýváme Uri tohoto kontejneru blob Base Uri. Každá revize vaší testovacího prostředí má svůj vlastní kontejner objektů blob, a proto každá revize vaší testovací hodu má vlastní základní identifikátor Uri. Testovací jednotka může předat základní uri rozbaleného balíčku nasazení do šablony prostřednictvím parametrů šablony.

## <a name="transforming-template-examples-for-test-drive"></a>Příklady transformace šablon pro testovací disku

Proces přeměny architektury prostředků na šablonu Správce prostředků testovací jednotky může být skličující. Abychom tento proces usnadnili,\'udělali jsme zde příklady, jak nejlépe [transformovat aktuální šablony nasazení](./transforming-examples-for-test-drive.md).

## <a name="how-to-publish-a-test-drive"></a>Jak publikovat testovací jízdu

Teď, když máte vytvořenou testovací jízdu, tato část provede každé z polí, které jsou pro úspěšné publikování testovací jednotky nutné.

![Povolení testovací jízdy v uživatelském rozhraní](./media/azure-resource-manager-test-drive/howtopub1.png)

První a nejdůležitější pole je přepnout, zda chcete Test Drive povoleno pro vaši nabídku, nebo ne. Vyberete-li **možnost Ano,** zobrazí se zbývající část formuláře se všemi požadovanými poli, která můžete vyplnit. Když vyberete **Ne,** formulář se zakáže a pokud znovu publikujete se zakázanou testovací jednotkou, testovací jednotka se odebere z produkčního prostředí.

Poznámka: Pokud existují nějaké testy jednotky aktivně používané uživateli, tyto testovací jednotky budou nadále spouštět, dokud jejich relace vyprší.

### <a name="details"></a>Podrobnosti

Další část, kterou chcete vyplnit, jsou podrobnosti o nabídce testovací houšti.

![Podrobné informace o testovací mase](./media/azure-resource-manager-test-drive/howtopub2.png)

**Popis -** *Povinné* Toto je místo, kde píšete hlavní popis o tom, co je na testovací jízdě. Zákazník si sem přijde přečíst, jaké scénáře bude vaše testovací jízda o vašem produktu zahrnovat. 

**Uživatelská příručka –** *povinné* Toto je podrobný návod k testovací jízdě. Zákazník to otevře a může projít přesně to, co chcete, aby dělali po celou dobu jejich testovací jízdy. Je důležité, aby tento obsah byl snadno pochopitelný a sledovat! (Musí se jednat o soubor PDF)

**Test Drive Demo Video -** *Doporučeno* Podobně jako v uživatelské příručce, je nejlepší zahrnout instruktážní video o vašem zážitku z testovací jízdy. Zákazník bude sledovat to před nebo během jejich Zkušební jízdy a může projít přesně to, co chcete, aby dělali v průběhu jejich Zkušební jízdy. Je důležité, aby tento obsah byl snadno pochopitelný a sledovat!

- **Název** – název videa
- **Odkaz** - Musí být vložené URL z trubice nebo videa. Příklad, jak získat vloženou adresu URL, je následující:
- **Miniatura** – musí se jednat o obraz ve vysoké kvalitě (533 × 324) pixelů. Doporučujeme, abyste zde pořídili snímek obrazovky s některými částmi prostředí testování.

Níže je uvedeno, jak se tato pole zobrazí pro zákazníka během jeho zkušeností s testovací mitou.

![Umístění polí testovací jednotky v nabídce Marketplace](./media/azure-resource-manager-test-drive/howtopub4.png)

### <a name="technical-configuration"></a>Technická konfigurace

Další část, kterou chcete vyplnit, je, kde nahrajete šablonu Správce prostředků testovací jednotky a definujete, jak konkrétně vaše instance testovací jednotky fungují.

![](./media/azure-resource-manager-test-drive/howtopub5.png)

**Instance –** *povinné:* Toto je místo, kde nakonfigurujete, kolik instancí chcete, v jaké oblasti a jak rychle mohou zákazníci získat testovací jednotku.

- **Instance** – Oblasti Select je místo, kde si vyberete, kde se nasadí vaše šablona Správce prostředků testovací jednotky. Doporučujeme vybrat si jednu oblast, kde nejvíce očekáváte, že vaši zákazníci budou umístěni.
- **Hot** - počet instancí testovací jednotky, které jsou již nasazeny a čekají na přístup na vybranou oblast. Zákazníci mohou okamžitě přistupovat k této testovací jednotky, spíše než muset čekat na nasazení. Kompromis emituje, že tyto instance jsou vždy spuštěné ve vašem předplatném Azure, takže jim vzniknou vyšší náklady na provozuna. Důrazně doporučujeme mít **alespoň jednu instanci Hot**, protože většina vašich zákazníků nechce čekat na dokončení úplného nasazení, a proto dochází k poklesu v používání zákazníků.
- **Teplý** – počet instancí testovací jednotky na oblast, které byly nasazeny a pak virtuální počítač byl zastaven a uložen v úložišti Azure. Čekací doba pro instance Warm je pomalejší než hot instance, ale náklady na dobu uptime úložiště je také levnější.
- **Studená** – počet instancí testovací jednotky na oblast, které lze případně nasadit. Studené instance vyžadují, aby celá šablona Správce prostředků testovací jednotky prošla nasazením v době, kdy zákazník požaduje testovací jednotku, takže je pomalejší než instance Hot nebo Warm. Kompromis emituje však, že musíte platit pouze po dobu trvání testovací jízdy.

V tomto okamžiku vypočítá celkový počet potenciálních souběžných testovacích jednotek, které budete zpřístupnit, a ověří, zda limit kvóty pro vaše předplatné zvládne tuto souběžnou částku:

**(Počet vybraných oblastí x aktivních instancí) + (Počet vybraných oblastí x teplých instancí) + (Počet vybraných oblastí x studených instancí)**

**Doba trvání testovací jednotky (hodiny) –** *požadovaná* doba trvání, po kterou bude testovací jízda aktivní, během \# několika hodin. Testovací cesta se automaticky ukončí po uplynutí tohoto časového období.

**Šablona Správce prostředků testovací jednotky –** *povinné* nahrát šablonu Správce prostředků sem. Jedná se o soubor, který jste vytvořili v předchozí části výše. Pojmenujte hlavní soubor šablony: "main-template.json" a ujistěte se, že šablona Správce prostředků obsahuje výstupní parametry pro klíčové proměnné, které jsou potřebné. (Musí se jednat o soubor ZIP)

**Přístup k informacím –** *požadováno* Poté, co zákazník získá testovací jízdu, jsou jim předloženy přístupové informace. Tyto pokyny jsou určeny ke sdílení užitečných výstupních parametrů ze šablony Správce prostředků testovací jednotky. Chcete-li zahrnout výstupní parametry, použijte dvojité složené závorky (například **{{outputname}}** a budou vloženy správně do umístění. (Zde se doporučuje formátování řetězce HTML, které se vykresluje v přední části).

### <a name="test-drive-deployment-subscription-details"></a>Podrobnosti o předplatném nasazení testovací jednotky

Poslední část, kterou chcete vyplnit, je možnost nasadit testovací jednotky automaticky připojením předplatného Azure a služby Azure Active Directory (AD).

![Podrobnosti o předplatném nasazení testovací jednotky](./media/azure-resource-manager-test-drive/subdetails1.png)

**ID předplatného Azure –** *povinné* To uděluje přístup ke službám Azure a portálu Azure. Předplatné je místo, kde se účtuje využití prostředků a služby se účtují. Pokud ještě nemáte **samostatné** předplatné Azure pro testovací jednotky, pokračujte a vytvořte si jedno. Id předplatného Azure najdete tak, že se přihlásíte na portál Azure a přejdete na předplatná v nabídce na levé straně. (Příklad: "a83645ac-1234-5ab6-6789-1h234g764ghty")

![Předplatná Azure](./media/azure-resource-manager-test-drive/subdetails2.png)

**ID klienta Azure AD –** *povinné,* pokud máte ID klienta,\> které je už k dispozici, najdete ho níže v id služby Vlastnosti – adresář.

![Vlastnosti služby Azure Active Directory](./media/azure-resource-manager-test-drive/subdetails3.png)

V opačném případě vytvořte nového klienta ve službě Azure Active Directory.

![Seznam klientů služby Azure Active Directory](./media/azure-resource-manager-test-drive/subdetails4.png)

![Definování organizace, domény a země nebo oblasti pro klienta Azure AD](./media/azure-resource-manager-test-drive/subdetails5.png)

![Potvrzení výběru](./media/azure-resource-manager-test-drive/subdetails6.png)

**ID aplikace Azure AD –** *povinný* další krok je vytvoření a registrace nové aplikace. Tuto aplikaci použijeme k provádění operací s instancí testovací jednotky.

1. Přejděte do nově vytvořeného adresáře nebo již existujícího adresáře a v podokně filtrů vyberte adresář Azure Active Directory.
2. Hledat "Registrace aplikací" a klikněte na "Přidat"
3. Zadejte název aplikace.
4. Vyberte typ jako "Webová aplikace / ROZHRANÍ API"
5. V přihlašovací adrese URL uveďte\'libovolnou hodnotu, toto pole nebudeme používat.
6. Klikněte na vytvořit.
7. Po vytvoření aplikace přejděte na\> Vlastnosti – Nastavte aplikaci jako víceklientské a stiskněte tlačítko Uložit.

Klikněte na Uložit. Posledním krokem je uchopit ID aplikace pro tuto registrovanou aplikaci a vložit ji do pole Test Drive zde.

![Podrobnosti id aplikace Azure AD](./media/azure-resource-manager-test-drive/subdetails7.png)

Vzhledem k tomu, že používáme aplikaci k nasazení do předplatného, musíme přidat aplikaci jako přispěvatele na předplatné. Pokyny pro tyto jsou následující:

1. Přejděte do okna Předplatná a vyberte příslušné předplatné, které používáte pouze pro testovací verzi.
1. Klikněte na **Řízení přístupu (IAM)**.
1. Klikněte na kartu **Přiřazení rolí.**  ![Přidání nového objektu řízení přístupu](./media/azure-resource-manager-test-drive/SetupSub7_1.jpg)
1. Klepněte na tlačítko **Přidat přiřazení role**.
1. Nastavte roli **přispěvatele**.
1. Zadejte název aplikace Azure AD a vyberte aplikaci, která roli přiřadí.
    ![Přidání oprávnění](./media/azure-resource-manager-test-drive/SetupSub7_2.jpg)
1. Klikněte na **Uložit**.

**Klíč aplikace Azure AD –** *povinné* Konečné pole je generovat ověřovací klíč. V části klíče přidejte popis klíče, nastavte dobu trvání na nikdy nevyprší a vyberte uložit. Je **důležité,** aby se zabránilo mít vypršela klíč, který zlomí zkušební jízdu v produkčním prostředí. Zkopírujte tuto hodnotu a vložte ji do požadovaného pole Testovací jednotka.

![Zobrazuje klíče pro aplikaci Azure AD](./media/azure-resource-manager-test-drive/subdetails8.png)

## <a name="next-steps"></a>Další kroky

Teď, když máte vyplněná všechna pole testovací jednotky, projděte a **znovu publikujte** svou nabídku. Jakmile vaše testovací verze projde certifikací, měli byste v **náhledu** vaší nabídky projít rozsáhlým testem zkušeností zákazníků. Spusťte testovací disk v unovém rozhraní a pak otevřete předplatné Azure na webu Azure Portal a ověřte, že vaše testovací jednotky jsou plně nasazeny správně.

![portál Azure](./media/azure-resource-manager-test-drive/subdetails9.png)

Je důležité si uvědomit, že neodstraníte žádné instance testovací jednotky, protože jsou zřízeny pro vaše zákazníky, takže služba Test Drive tyto skupiny prostředků automaticky vyčistí po dokončení zákazníka.

Jakmile se budete cítit pohodlně s nabídkou Náhled, nyní je čas **jít žít!** Po publikování nabídky je proveden proces závěrečné kontroly od společnosti Microsoft, aby bylo možné zkontrolovat celou dobu od začátku do konce. Pokud z nějakého důvodu bude nabídka odmítnuta, zašleme technickému kontaktu pro vaši nabídku oznámení, ve kterém vysvětlíme, co bude třeba opravit.

Máte-li další dotazy, hledáte rady pro řešení potíží nebo chcete, aby byla vaše testovací jízda úspěšnější, přejděte na [nejčastější dotazy, řešení potíží & doporučené postupy](./marketing-and-best-practices.md).
