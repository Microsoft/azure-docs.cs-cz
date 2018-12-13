---
title: Jednotky Test aplikace logiky | Dokumentace Microsoftu
description: Vysvětluje, jak vytvářet jejich testovací verze, která se připojuje k instanci Dynamics AX/CRM nebo jiný prostředek mimo Azure stejně.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: pbutlerm
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: pbutlerm
ms.openlocfilehash: d784941c43da13a2c1bd120599aa02fe14e5a5b4
ms.sourcegitcommit: 1c1f258c6f32d6280677f899c4bb90b73eac3f2e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/11/2018
ms.locfileid: "53258198"
---
<a name="logic-app-test-drive"></a>Jednotky Test aplikace logiky
====================

Tento článek je určený pro vydavatele, kteří mají jejich nabídek zveřejněných na AppSource a chcete vytvořit své testovací verze, která se připojuje k instanci Dynamics AX/CRM nebo jiný prostředek mimo Azure jenom.

<a name="how-to-build-a-logic-app-test-drive"></a>Jak vytvořit jednotku Test aplikace logiky
-----------------------------------

Testovací jednotky dokumentaci pro logiku aplikace testů jednotek je aktuálně stále na Githubu pro [operace](https://github.com/Microsoft/AppSource/blob/master/Setup-your-Azure-subscription-for-Dynamics365-Operations-Test-Drives.md) a [Customer Engagement](https://github.com/Microsoft/AppSource/wiki/Setting-up-Test-Drives-for-Dynamics-365-app)najdete zde přečíst více.

<a name="how-to-publish-a-test-drive"></a>Jak publikovat testovací verze
---------------------------

Teď, když máte vaše testovací verze vytvořené, tato část vás provede každé pole, které potřebujete k úspěšnému publikování vaše testovací verze.

![Povolit funkci testovací verze](./media/azure-resource-manager-test-drive/howtopub1.png)

Můžete určit, jestli chcete, aby Test s všechna požadovaná pole ve formuláři se zobrazí za vás o vyplnění je první a nejdůležitější pole. Když vyberete **Ne,** formuláře bude zakázáno a opětovné publikování pomocí testovací verze zakázán, vaše testovací verze se odebere z produkčního prostředí.

*Poznámka:*: Pokud neexistuje žádné testovací verze aktivně používá uživatelů, tyto testovací verze bude pokračovat ke spuštění, dokud nevyprší jejich relace.

### <a name="details"></a>Podrobnosti

Další části a vyplňte je že nabízejí podrobné informace o vaše testovací verze.

![Podrobnosti o ovladači testu](./media/azure-resource-manager-test-drive/howtopub2.png)

**Popis –** *[povinné pole]* Toto je místo, kde píšete hlavní popis o tom, co je na vaše testovací verze. Zákazník Sem přijde na jaké scénáře vaše testovací verze se zahrnující informace o tomto produktu. 

**Uživatelská příručka -** *[povinné pole]* Toto je podrobný návod prostředí Test Drive. Zákazník se otevře toto a můžete projít přesně co chcete udělat v rámci jejich testovací verze. Je důležité, že tento obsah je snadno srozumitelný a postupujte podle! (Musí být soubor PDF)

**Test jednotky – ukázkové Video -** \[doporučená\] podobně jako v uživatelské příručce, je nejlepší zahrnout videokurz prostředí Test Drive. Zákazník bude sledovat tuto předem nebo během jejich testovací jízdu a můžete projít přesně co chcete udělat v rámci jejich testovací verze. Je důležité, že tento obsah je snadno srozumitelný a postupujte podle!

- **Název** – název videa
- **Odkaz** – musí být vložený adresa URL z YouTube nebo Vimeo. Příklad toho, jak získat adresu url vložené je nižší než:
- **Miniatura** – musí být vysoce kvalitní image pixelů (533 x 324). Doporučujeme pořídit snímek některých součástí prostředí Test Drive tady.

Níže je, jak tato pole objeví pro vaše zákazníky během jejich testovací verze.

![Test jednotky pole vzhled a chování](./media/azure-resource-manager-test-drive/howtopub4.png)

### <a name="technical-configuration"></a>Technické konfigurace

Další části a vyplňte je můžete konfigurovat aplikaci logiky testovací jednotky a konkrétně jak definovat vaše testovací verze instance práce.

![Technické konfiguraci testu jednotky](./media/azure-resource-manager-test-drive/howtopub5_logicapp.png)

- **Oblast** - *[vyžaduje pole]* oblasti je vybrat je, kde vyberete, ve které jsou nasazené prostředky Test Drive aplikace logiky v.

    *Poznámka:* Pokud vaše aplikace logiky má všechny vlastní prostředky, které jsou uloženy v oblasti, ujistěte se, že se tady vyberete danou oblast. Je nejlepší způsob, jak to udělat **plně nasadit svou aplikaci logiky místně v rámci předplatného Azure na portálu a ověřte, zda funguje** před zápisem tady.

- **Maximální souběžných testovacími verzemi** - *[vyžaduje pole]* instance číslo jednotky testů, které už jsou nasazené a čeká se na přístup za vybrané oblasti. Zákazníci mají okamžitě přístup k tento Test jednotky a nemusíte čekat pro nasazení.

    *Poznámka:* Pokud používáte webinář/třídy místo všechny studenty, aby si testovací jízdu číslo N, se doporučuje pro publikování s číslem N horké instancí a pak jednou třída je více než znovu publikovat zpět na vaše číslo normální výměně instancí.

- **Jednotka doba trvání testu (hodiny) -** *[vyžaduje pole]* dobu jak dlouho Test Drive zůstane aktivní, v \# hodin. Po skončení tohoto období se automaticky ukončí na testovací verzi.

- **Název skupiny prostředků Azure -** *[povinné pole]* psát název skupiny prostředků, kde jsou uloženy vaše Logic App testovací verze.

- **Přiřaďte název aplikace logiky –** *[povinné pole]* zápisu psát logiku aplikace, která slouží k přiřazení uživatele v testovací verze před zákazníka, získá název této logiky aplikace. Ujistěte se, že tento soubor je uložen ve skupině prostředků výše.

- **Název aplikace logiky – zrušení zřízení** *[povinné pole]* zápisu v názvu aplikace logiky pro vaše zrušení všechny prostředky vytvořené v testovací verze. Ujistěte se, že tento soubor je uložen ve skupině prostředků výše.

- **Přístup k informacím o -** *[povinné pole]* po zákazník dostane jejich Test Drive, se jim prezentují přístup k informacím. Tyto pokyny jsou určené ke sdílení užitečné výstupní parametry z testovacího správce prostředků disku šablony. Chcete-li zahrnout výstupních parametrů, použijte dvojitých složených závorkách (například **{{outputname}}**), a že bude správně vložena do umístění. (Formátování řetězce ve formátu HTML je zde doporučeného k vykreslení v části front end).

### <a name="test-drive-deployment-subscription-details"></a>Podrobnosti předplatného nasazení testu jednotky

Závěrečné sekci o vyplnění je testovací verze automatické nasazení propojením vaše předplatné Azure a Azure Active Directory (AD).

![Podrobnosti o předplatném jednotku nasazení testu](./media/azure-resource-manager-test-drive/subdetails1.png)

**ID předplatného Azure** *[vyžaduje pole]* tím udělíte přístup ke službám Azure a webu Azure portal. Předplatné je, když se použije v hlášení využití prostředků a služby se účtují. Pokud již nemáte **samostatné** předplatné Azure pro Test jednotek pouze prosím pokračujte a provádějte jeden. ID předplatného Azure můžete najít tak, že přihlášení k webu Azure portal a navigace k předplatným v levé nabídce.
(Příklad: "a83645ac-1234-5ab6-6789-1h234g764ghty")

![Předplatná Azure](./media/azure-resource-manager-test-drive/subdetails2.png)

**ID Tenanta Azure AD** *[vyžaduje pole]* máte již k dispozici ID Tenanta najdete ho pod ve vlastnostech -\> ID adresáře.

![Azure Active Directory](./media/azure-resource-manager-test-drive/subdetails3.png)

V opačném případě vytvořte nového Tenanta v Azure Active Directory.

![Azure Active Directory vlastnosti obrazovky](./media/azure-resource-manager-test-drive/subdetails4.png)

! Azure Active Directory](./media/azure-resource-manager-test-drive/subdetails5.png)

![Klienti služby Azure Active Directory](./media/azure-resource-manager-test-drive/subdetails6.png)

**ID aplikace Azure AD** *[vyžaduje pole]* dalším krokem je vytvoření a registrace nové aplikace. Tuto aplikaci budeme používat k provádění operací ve vaší instanci testovací verze.

1. Přejděte do adresáře s nově vytvořený nebo již existující adresář a Azure Active directory, vyberte v podokně filtru.
2. Najděte "Registrace aplikací" a klikněte na "Add"
3. Zadejte název aplikace.
4. Vyberte typ jako "webové aplikace a rozhraní API"
5. Zadejte libovolnou hodnotu v přihlašovací adresa URL, vyhráli jsme\'t používá toto pole.
6. Klikněte na vytvořit.
7. Po vytvoření aplikace, přejděte do vlastností -\> nastavení aplikace jako víceklientské kliknu na Uložit.

Klikněte na Uložit. Posledním krokem je zkopírovat ID aplikace pro tuto registrované aplikaci a vložte ho do pole testovací verze.

![ID aplikace Azure Active Directory](./media/azure-resource-manager-test-drive/subdetails7.png)

Zadané aplikace se používá k nasazení do předplatného, potřebujeme pro tuto aplikaci přidat jako přispěvatele daného předplatného. Tyto pokyny jsou jako níže:

1. Přejděte do okna odběry a vyberte odpovídající předplatné, který používáte pro pouze na testovací verzi.
1. Klikněte na tlačítko **řízení přístupu (IAM)**.
1. Klikněte na tlačítko **přiřazení rolí** kartu.  ![Azure Active Directory, přidání nového objektu zabezpečení řízení přístupu](./media/azure-resource-manager-test-drive/SetupSub7_1.jpg)
1. Klikněte na tlačítko **přidat přiřazení role**.
1. Nastavit jako roli **Přispěvatel**.
1. Zadejte název aplikace Azure AD a vyberte aplikaci, pokud chcete přiřadit roli.
    ![Oprávnění Azure Active Directory](./media/azure-resource-manager-test-drive/SetupSub7_2.jpg)
1. Klikněte na **Uložit**.

**Klíč aplikace Azure AD –** *[povinné pole]* poslední pole má generovat ověřovací klíč. V části klíče přidejte popis klíče, nastavte dobu trvání nikdy nevyprší, pak vyberte možnost uložit. Je **důležité** abyste se vyhnuli nutnosti vypršela klíčů, což naruší vaše testovací verze v produkčním prostředí. Zkopírujte tuto hodnotu a vložte ho do vašeho povinné pole testovací verze.

![Azure Active Directory s klíči](./media/azure-resource-manager-test-drive/subdetails8.png)

<a name="next-steps"></a>Další postup
----------

Teď, když máte všechna vaše testovací verze pole doplnit, projděte si a **znovu publikovat** vaší nabídky. Jakmile se vaše testovací verze uplynutí procesu certifikace, byste se měli zúčastnit rozsáhlé testování prostředí pro zákazníky v **ve verzi preview** o vaší nabídce. Spusťte si testovací jízdu v uživatelském rozhraní a ověřte, že vaše testovací verze se plně nasazují správně.

Je důležité si uvědomit, že neodstraníte libovolné části Test Drive jako byly povolené pro vaše zákazníky, tak službu Test Drive automaticky vyčistí tyto skupiny prostředků po dokončení s ním zákazníka.

Jakmile budete s klidem s vaší nabídkou ve verzi Preview, nyní je čas **vysílat živě**! Po nabídky byl publikován do dvojitých vrácení celé prostředí koncového je posledním kontrolním procesu od Microsoftu. Pokud z nějakého důvodu získá zamítl nabídky, pošleme na technický kontakt pro vaši nabídku s vysvětlením, co bude potřebovat dojít k opravě oznámení.

Pokud máte další otázky, hledají Poradce při potížích nebo chtít, aby vaše testovací verze úspěšnější, přejděte prosím na [nejčastější dotazy, řešení potíží a osvědčené postupy](./marketing-and-best-practices.md).
