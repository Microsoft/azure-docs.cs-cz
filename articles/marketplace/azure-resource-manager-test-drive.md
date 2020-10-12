---
title: Typy testovacích jednotek, Microsoft Commercial Marketplace
description: Typy testovacích jednotek na komerčním webu Marketplace
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
ms.date: 06/19/2020
ms.author: keferna
author: keferna
ms.openlocfilehash: 92fd4d629585ed465e2891be2dce1c1bdc8c88e6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "87287941"
---
# <a name="azure-resource-manager-test-drive"></a>Azure Resource Manager testovacích jednotek

Tento typ použijte, pokud máte nabídku Azure Marketplace nebo AppSource, ale chcete vytvořit testovací jednotku s pouze prostředky Azure. Šablona Azure Resource Manager (ARM) je kódovaný kontejner prostředků Azure, který navrhujete, aby nejlépe představoval vaše řešení. Test Drive vezme zadanou šablonu ARM a nasadí všechny prostředky, které vyžaduje, do skupiny prostředků. Toto je jediná možnost testovacího disku pro virtuální počítač nebo nabídky aplikací Azure.

Pokud nejste obeznámeni s tím, co je šablona ARM, přečtěte si téma [co je Azure Resource Manager?](../azure-resource-manager/resource-group-overview.md) a [Pochopte strukturu a syntaxi šablon ARM](../azure-resource-manager/resource-group-authoring-templates.md) , abyste lépe pochopili, jak vytvářet a testovat vlastní šablony.

Informace o **hostované** nebo testovací jednotce **Aplikace logiky** najdete v tématu [co je testovací jednotka?](what-is-test-drive.md)

## <a name="technical-configuration"></a>Technická konfigurace

Šablona nasazení obsahuje všechny prostředky Azure, které tvoří vaše řešení. Produkty, které vyhovují tomuto scénáři, používají pouze prostředky Azure. V partnerském centru nastavte následující vlastnosti:

- **Oblasti** (povinné) – v současné době je 26 oblastí podporovaných v Azure, ve kterých se dá vaše testovací jednotka zpřístupnit. Obvykle budete chtít, aby byla testovací jednotka dostupná v oblastech, kde očekáváte největší počet zákazníků, aby mohli vybrat nejbližší oblast pro nejlepší výkon. Budete se muset ujistit, že vaše předplatné bude mít povolené nasazení všech prostředků potřebných v každé z oblastí, které vyberete.

- **Instance** – vyberte typ (horká nebo studená) a počet dostupných instancí, které se vynásobí počtem oblastí, ve kterých je vaše nabídka dostupná.

  - **Hot** – tento typ instance je nasazený a očekává se přístup na vybranou oblast. Zákazníci můžou okamžitě přistupovat k *horkým* instancím testovacích jednotek, ale nemusíte čekat na nasazení. Kompromisem je, že tyto instance jsou vždycky spuštěné v předplatném Azure, takže budou mít větší náklady na dobu provozu. Důrazně doporučujeme mít aspoň jednu *aktivní* instanci, protože většina zákazníků nechce čekat na plná nasazení, takže když není k dispozici žádná *Hot* instance, výsledkem je vypnutí v zákaznickém využívání.

  - **Studená** – tento typ instance představuje celkový počet instancí, které mohou být nasazeny v jednotlivých oblastech. Studené instance vyžadují pro nasazení celé testovací jednotky Správce prostředků šablonu, když zákazník požádá o testovací verzi, takže *studené* instance budou mnohem pomalejší, než se dosadí *za horké* instance. Kompromisy je, že je nutné platit jenom za dobu trvání testovacích jednotek *, ale v* předplatném Azure není vždycky spuštěná jako s *horkou* instancí.

- **Test drive Azure Resource Manager šablona** – nahrajte soubor. zip obsahující šablonu Azure Resource Manager. Další informace o vytvoření šablony Azure Resource Manager v článku rychlý Start [vytváření a nasazování Azure Resource Manager šablon pomocí Azure Portal](../azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal.md).

- **Doba trvání testovacích jednotek** (požadováno) – zadejte počet hodin, po který bude testovací jednotka aktivní. Testovací jednotka se po skončení tohoto časového období ukončí automaticky. Používejte pouze celá čísla (například "2" hodiny jsou platné, "1,5" není).

## <a name="write-the-test-drive-template"></a>Zapsat šablonu testovacího disku

Jakmile budete mít k dispozici požadovaný balíček prostředků, zapište a sestavte šablonu služby Test Drive ARM. Vzhledem k tomu, že testovací verze spouští nasazení v plně automatizovaném režimu, šablony testovacích jednotek mají určitá omezení:

### <a name="parameters"></a>Parametry

Většina šablon má sadu parametrů, které definují názvy prostředků, velikosti prostředků (například typy účtů úložiště nebo virtuálních počítačů), uživatelská jména a hesla, názvy DNS atd. Když nasadíte řešení pomocí Azure Portal, můžete ručně naplnit všechny tyto parametry, vybrat dostupné názvy DNS nebo názvy účtů úložiště a tak dále.

![Seznam parametrů v Azure Resource Manager](media/test-drive/resource-manager-parameters.png)

Testovací jednotka ale funguje automaticky bez lidské interakce, takže podporuje jenom omezené sady kategorií parametrů. Pokud parametr v šabloně ARM testovacích jednotek nepatří do jedné z podporovaných kategorií, je nutné nahradit tento parametr proměnnou nebo konstantní hodnotou.

Pro parametry můžete použít libovolný platný název; testovací jednotka rozpoznává kategorii parametru pomocí hodnoty typu metadat. Zadejte metadata-Type pro každý parametr šablony, jinak se vaše šablona nepředá ověření:

```JSON
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

> [!NOTE]
> Všechny parametry jsou volitelné, takže pokud nechcete použít žádné, nemusíte být.

### <a name="accepted-parameter-metadata-types"></a>Přijaté typy metadat parametrů

| Typ metadat   | Typ parametru  | Description     | Ukázková hodnota    |
|---|---|---|---|
| **identifikátor**     | řetězec          | Základní identifikátor URI balíčku pro nasazení| `https:\//\<\..\>.blob.core.windows.net/\<\..\>` |
| **jmen**    | řetězec          | Nové náhodné uživatelské jméno.| admin68876      |
| **heslo**    | zabezpečený řetězec    | Nové náhodné heslo | LP! ACS \^ 2kH     |
| **ID relace**   | řetězec          | Jedinečný identifikátor relace testovacích jednotek (GUID)    | b8c8693e-5673-449c-badd-257a405a6dee |

#### <a name="baseuri"></a>identifikátor

Test Drive Inicializuje tento parametr se **základním identifikátorem URI** balíčku pro nasazení, abyste mohli pomocí tohoto parametru vytvořit identifikátor URI libovolného souboru, který je součástí balíčku.

```JSON
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

Tento parametr použijte v rámci šablony, chcete-li vytvořit identifikátor URI libovolného souboru z balíčku pro nasazení testovacích jednotek. Následující příklad ukazuje, jak vytvořit identifikátor URI propojené šablony:

```JSON
"templateLink": {
  "uri": "[concat(parameters('baseuri'),'templates/solution.json')]",
  "contentVersion": "1.0.0.0"
}
```

#### <a name="username"></a>username

Test Drive Inicializuje tento parametr pomocí nového náhodného uživatelského jména:

```JSON
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

Ukázková hodnota: `admin68876`

Pro vaše řešení můžete použít buď náhodná, nebo trvalá uživatelská jména.

#### <a name="password"></a>heslo

Test Drive Inicializuje tento parametr pomocí nového náhodného hesla:

```JSON
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

Ukázková hodnota:  `Lp!ACS^2kh`

Pro vaše řešení můžete použít náhodná nebo trvalá hesla.

#### <a name="session-id"></a>ID relace

Test Drive Inicializuje tento parametr s jedinečným identifikátorem GUID, který představuje ID relace testovací jednotky:

```JSON
"parameters": {
  ...
  "sessionid": {
    "type": "string",
    "metadata": {
      "type": "sessionid",
      "description": "Unique test drive session id."
    }
  },
  ...
}
```

Ukázková hodnota: `b8c8693e-5673-449c-badd-257a405a6dee`

Tento parametr můžete použít k jednoznačné identifikaci relace testovacích jednotek, pokud je to nezbytné.

### <a name="unique-names"></a>Jedinečné názvy

Některé prostředky Azure, jako jsou účty úložiště nebo názvy DNS, vyžadují globálně jedinečné názvy. To znamená, že pokaždé, když testovací jednotka nasadí šablonu ARM, vytvoří novou skupinu prostředků s jedinečným názvem pro všechny její prostředky. Proto je nutné použít funkci [uniquestring](../azure-resource-manager/templates/template-functions.md) zřetězenou s názvy proměnných v ID skupiny prostředků pro generování náhodných jedinečných hodnot:

```JSON
"variables": {
  ...
  "domainNameLabel": "[concat('contosovm',uniquestring(resourceGroup().id))]",
  "storageAccountName": "[concat('contosodisk',uniquestring(resourceGroup().id))]",
  ...
}
```

Zajistěte zřetězení parametrů nebo proměnných řetězců ( `contosovm` ) s jedinečným výstupem řetězce ( `resourceGroup().id` ), protože to zaručuje jedinečnost a spolehlivost každé proměnné.

Například většina názvů prostředků nemůže začínat číslicí, ale funkce Unique String může vracet řetězec, který začíná číslicí. Takže pokud použijete nezpracovaný jedinečný výstup řetězce, vaše nasazení se nezdaří.

Další informace o pravidlech a omezeních názvů prostředků najdete v [tomto článku](/azure/cloud-adoption-framework/ready/azure-best-practices/naming-and-tagging).

### <a name="deployment-location"></a>Umístění nasazení

K dispozici je možnost testovat jednotku v různých oblastech Azure. Cílem je umožnit uživateli vybrat nejbližší oblast, která bude poskytovat touchdown uživatelské prostředí.

Když testovací jednotka vytvoří instanci testovacího prostředí, vždy vytvoří skupinu prostředků v oblasti, kterou vybere uživatel, a pak v tomto kontextu skupiny spustí šablonu nasazení. Proto by šablona měla vybrat umístění nasazení ze skupiny prostředků:

```JSON
"variables": {
  ...
  "location": "[resourceGroup().location]",
  ...
}
```

A pak použijte toto umístění pro všechny prostředky konkrétní instance testovacího prostředí:

```JSON
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

Ujistěte se, že vaše předplatné může v každé oblasti, kterou vyberete, nasadit všechny požadované prostředky. Ujistěte se také, že vaše image virtuálních počítačů jsou dostupné ve všech oblastech, které povolíte, jinak šablona nasazení nebude pro některé oblasti fungovat.

### <a name="outputs"></a>Výstupy

Normálně se šablonami Správce prostředků můžete nasadit bez nutnosti vytvářet výstup. Je to proto, že znáte všechny hodnoty, které použijete k naplnění parametrů šablony, a můžete vždy ručně zkontrolovat vlastnosti libovolného prostředku.

V případě Správce prostředků šablon testovacích jednotek je však důležité vrátit se k testovací jednotce všechny informace, které jsou vyžadovány pro získání přístupu k testovacímu prostředí (identifikátory URI webů, názvy hostitelů virtuálních počítačů, uživatelská jména a hesla). Zajistěte, aby všechny vaše výstupní názvy byly čitelné, protože tyto proměnné jsou prezentovány zákazníkovi.

Neexistují žádná omezení související s výstupem šablony. Test Drive převede všechny výstupní hodnoty na řetězce, takže pokud odešlete objekt do výstupu, uživatel uvidí řetězec JSON.

Příklad:

```JSON
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

Nezapomeňte na omezení předplatného a služeb. Pokud například chcete nasadit až 10 4 virtuálních počítačů, musíte zajistit, aby předplatné, které používáte pro vaše testovací prostředí, umožňovalo používat 40 jader. Další informace o omezeních předplatného a služeb Azure najdete v tématu [limity, kvóty a omezení předplatného a služeb Azure](../azure-resource-manager/management/azure-subscription-service-limits.md). Když lze současně provést více testovacích jednotek, ověřte, že vaše předplatné může zpracovávat počet jader vynásobený celkovým počtem souběžných testovacích jednotek, které lze provést.

### <a name="what-to-upload"></a>Co nahrát

Šablona nástroje Test Drive ARM se nahraje jako soubor zip, který může obsahovat různé artefakty nasazení, ale musí mít jeden soubor s názvem `main-template.json` . Toto je šablona nasazení ARM, kterou testovací jednotka používá k vytvoření instance testovacího prostředí. Pokud máte další prostředky, které jsou nad tímto souborem, můžete na ně odkazovat jako na externí prostředky v rámci šablony nebo je zahrnout do souboru ZIP.

Během certifikace publikování testovací jednotka rozbalí balíček pro nasazení a vloží jeho obsah do kontejneru objektů BLOB interního testovacího disku. Struktura kontejneru odráží strukturu balíčku pro nasazení:

| package.zip                       | Kontejner objektů BLOB testovací jednotky         |
|---|---|
| `main-template.json`                | `https:\//\<\...\>.blob.core.windows.net/\<\...\>/main-template.json`  |
| `templates/solution.json`           | `https:\//\<\...\>.blob.core.windows.net/\<\...\>/templates/solution.json` |
| `scripts/warmup.ps1`                | `https:\//\<\...\>.blob.core.windows.net/\<\...\>/scripts/warmup.ps1`  |
|||

Voláme identifikátor URI tohoto základního identifikátoru URI kontejneru objektů BLOB. Vzhledem k tomu, že každá revize vašeho testovacího prostředí má vlastní kontejner objektů blob, má každá revize vašeho testovacího prostředí svůj vlastní základní identifikátor URI. Testovací jednotka může předat základní identifikátor URI vašeho balíčku pro nasazení unzip do šablony prostřednictvím parametrů šablony.

### <a name="transform-template-examples-for-test-drive"></a>Příklady šablon transformace pro Test Drive

Proces zapnutí architektury prostředků do testovací jednotky Správce prostředků šablonou může být těžké. Další nápovědu najdete v těchto příkladech, jak nejlépe transformovat aktuální šablony nasazení v tématu [co je testovací jednotka?](what-is-test-drive.md#transforming-examples).

## <a name="test-drive-deployment-subscription-details"></a>Podrobnosti o předplatném nasazení testovacích jednotek

Poslední část, která se má dokončit, je umožnit automatické nasazení testovacích jednotek tím, že se připojíte k předplatnému Azure a Azure Active Directory (AD).

![Podrobnosti o předplatném nasazení testovacích jednotek](media/test-drive/deployment-subscription-details.png)

1. Získejte **ID předplatného Azure**. Tím se udělí přístup ke službám Azure a Azure Portal. Předplatné je místo, kde se oznamuje využití prostředků, a účtují se služby. Pokud ještě nemáte samostatné předplatné Azure pro testovací disky, udělejte ho. ID předplatných Azure (například) můžete najít `1a83645ac-1234-5ab6-6789-1h234g764ghty1` tak, že se přihlásíte Azure Portal a vyberete **odběry** z nabídky vlevo a navigovat.

   ![Předplatná Azure](media/test-drive/azure-subscriptions.png)

2. Získejte **ID tenanta Azure AD**. Pokud už máte ID tenanta k dispozici, najdete ho v **Azure Active Directory**  >  **vlastnosti**  >  **ID adresáře**:

   ![Vlastnosti Azure Active Directory](media/test-drive/azure-active-directory-properties.png)

   Pokud nemáte ID tenanta, vytvořte nový v Azure Active Directory. Nápovědu k nastavení tenanta najdete v tématu [rychlý Start: nastavení tenanta](../active-directory/develop/quickstart-create-new-tenant.md).

3. **ID aplikace Azure AD** – vytvořte a zaregistrujte novou aplikaci. Tuto aplikaci použijeme k provádění operací s instancí testovacích jednotek.

   1. Přejděte do nově vytvořeného adresáře nebo již existujícího adresáře a v podokně filtru vyberte Azure Active Directory.
   2. Vyhledejte **Registrace aplikací** a vyberte **Přidat**.
   3. Zadejte název aplikace.
   4. Vyberte **typ** **webové aplikace nebo rozhraní API**.
   5. Zadejte libovolnou hodnotu v přihlašovací adrese URL, toto pole se nepoužije.
   6. Vyberte **Vytvořit**.
   7. Po vytvoření aplikace vyberte možnost **vlastnosti**  >  **nastavit aplikaci jako více tenantů** a pak akci **uložte**.

4. Vyberte **Uložit**.

5. Zkopírujte ID aplikace pro tuto registrovanou aplikaci a vložte ji do pole testovací jednotka.

   ![Podrobnosti o ID aplikace Azure AD](media/test-drive/azure-ad-application-id-detail.png)

6. Vzhledem k tomu, že používáme aplikaci k nasazení do předplatného, musíme aplikaci přidat jako Přispěvatel v rámci předplatného:

   1. Vyberte typ **předplatného** , které používáte pro testovací jednotku.
   1. Vyberte **Řízení přístupu (IAM)** .
   1. Vyberte kartu **přiřazení rolí** a pak **Přidat přiřazení role**.

      ![Přidat nový objekt zabezpečení Access Control](media/test-drive/access-control-principal.jpg)

   1. Nastavte **roli** a **přiřaďte přístup k** , jak je uvedeno. Do pole **Vybrat** zadejte název aplikace Azure AD. Vyberte aplikaci, ke které chcete přiřadit roli **Přispěvatel** .

      ![Přidat oprávnění](media/test-drive/access-control-permissions.jpg)

   1. Vyberte **Uložit**.

7. Vygenerujte **aplikace Azure AD** ověřovací klíč. V části **klíče**přidejte **Popis klíče**, nastavte dobu trvání na bez **platnosti** (v případě, že vypršela platnost testovací jednotky v produkčním prostředí) a pak vyberte **Uložit**. Zkopírujte tuto hodnotu a vložte ji do pole požadovaných testovacích jednotek.

![Zobrazuje klíče pro aplikaci Azure AD.](media/test-drive/azure-ad-app-keys.png)

## <a name="republish"></a>Opětovné publikování

Teď, když jsou všechna vaše pole testovacích jednotek dokončená, **znovu publikujte** vaši nabídku. Po úspěšném ověření vaší zkušební verze otestujte prostředí pro zákazníky ve verzi Preview vaší nabídky:

1. Spusťte testovací jednotku v uživatelském rozhraní.
1. V Azure Portal otevřete své předplatné Azure.
1. Ověřte, zda je správně nasazování testovacích jednotek.

   ![portál Azure](media/test-drive/azure-portal.png)

Neodstraňujte žádné instance testovacích jednotek zřízené pro vaše zákazníky. Služba Test Drive po dokončení práce s ní automaticky vyčistí tyto skupiny prostředků.

Jakmile budete spokojeni s vaší nabídkou Preview, je čas **začít**. K dispozici je finální proces kontroly, který umožňuje dvakrát zkontrolovat celé prostředí. Pokud nabídku zamítneme, pošleme e-mail technickému kontaktu pro vaši nabídku s vysvětlením, co je potřeba opravit.

## <a name="next-steps"></a>Další kroky

- Pokud jste použili pokyny k vytvoření nabídky v partnerském centru, vraťte se k tomuto tématu pomocí šipky zpět.
- Další informace o dalších typech testovacích jednotek najdete v části [co je testovací jednotka?](what-is-test-drive.md).
