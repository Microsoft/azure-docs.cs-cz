---
title: Testovací jednotka aplikace logiky | Azure Marketplace
description: Vysvětluje, jak sestavit testovací jednotku, která se připojuje k instanci Dynamics AX/CRM nebo jinému prostředku mimo Azure.
services: Azure, Marketplace, Cloud Partner Portal,
author: pbutlerm
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: pabutler
ms.openlocfilehash: 87170344f7467770829cbd8ba7d8936a9e39c0cc
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/08/2019
ms.locfileid: "73824588"
---
<a name="logic-app-test-drive"></a>Testovací jednotka aplikace logiky
====================

Tento článek se používá pro vydavatele, kteří mají svou nabídku na AppSource a chtějí sestavit svoji testovací jednotku, která se připojuje k instanci Dynamics AX/CRM nebo jinému prostředku mimo Azure.

<a name="how-to-build-a-logic-app-test-drive"></a>Postup sestavení testovací jednotky aplikace logiky
-----------------------------------

Dokumentace ke zkušební jednotce pro testovací jednotky aplikace logiky je aktuálně stále na GitHubu pro [operace](https://github.com/Microsoft/AppSource/blob/master/Setup-your-Azure-subscription-for-Dynamics365-Operations-Test-Drives.md) a [zapojení zákazníka](https://github.com/Microsoft/AppSource/wiki/Setting-up-Test-Drives-for-Dynamics-365-app). Další informace najdete tady.

<a name="how-to-publish-a-test-drive"></a>Postup publikování testovacího disku
---------------------------

Teď, když máte vytvořenou testovací jednotku, Tato část vás provede všemi poli potřebnými k úspěšnému publikování testovacích jednotek.

![Povolení funkce Test Drive](./media/azure-resource-manager-test-drive/howtopub1.png)

Prvním a nejnejdůležitějším polem je přepnout, jestli chcete vyplnit test formuláře se všemi požadovanými poli. Když vyberete **Ne,** formulář se deaktivuje a pokud znovu publikujete s neaktivovanou testovací jednotkou, bude testovací jednotka z produkčního prostředí odebrána.

*Poznámka*: Pokud jsou k dispozici testovací jednotky, které uživatelé aktivně používají, budou se tyto testovací jednotky spouštět až do vypršení platnosti jejich relace.

### <a name="details"></a>Podrobnosti

Další část, která se má vyplnit, jsou podrobnosti o nabídce testovacích jednotek.

![Podrobnosti o testovacím ovladači](./media/azure-resource-manager-test-drive/howtopub2.png)

**Popis –** *[povinné pole]* sem napíšete hlavní popis toho, co je na testovacím disku. Zde se zobrazí zákazník, který si přečte scénáře, na které se vaše testovací jednotka vztahuje na váš produkt. 

**Ruční uživatel –** *[povinné pole]* Toto je podrobný návod k vašemu prostředí Test Drive. Zákazník ho otevře a bude moct procházet přesně to, co chcete dělat v rámci své testovací jednotky. Je důležité, abyste tento obsah snadno pochopili a následovali! (Musí se jednat o soubor. PDF)

**Ukázková zkušební verze testovacího videa –** \[Doporučené\] podobně jako ruční příručka pro uživatele, je nejlepší zahrnout video kurz prostředí Test Drive. Zákazník si tento postup předá nebo během testovacích jednotek dokončí a může procházet přesně to, co chcete v rámci své zkušební jednotky provádět. Je důležité, abyste tento obsah snadno pochopili a následovali!

- **Název** – název videa
- **Odkaz** – musí se jednat o VLOŽENOU adresu URL z YouTube nebo Vimeo. Příklad toho, jak získat vloženou adresu URL, je níže:
- **Miniatura** – musí to být obrázek s vysokou kvalitou (533x324) pixelů. Doporučujeme udělat snímek obrazovky některé z možností testování testovacích jednotek.

Níže je uveden způsob, jakým se tato pole zobrazí pro zákazníka během používání testovacích jednotek.

![Vzhled a chování polí testovacích jednotek](./media/azure-resource-manager-test-drive/howtopub4.png)

### <a name="technical-configuration"></a>Technická konfigurace

Další část, která se má vyplnit, je místo, kde konfigurujete svou aplikaci logiky testovacích jednotek a definujete, jak konkrétní instance testovacích jednotek fungují.

![Technická konfigurace testovacích jednotek](./media/azure-resource-manager-test-drive/howtopub5_logicapp.png)

- **Oblast** -  *[povinné pole]* oblast, kterou jste vybrali, je místo, kde můžete vybrat, kde jsou nasazené prostředky aplikace logiky testu.

    *Poznámka:* Pokud vaše aplikace logiky obsahuje nějaké vlastní prostředky, které jsou uložené v nějaké oblasti, ujistěte se, že je zde vybraná oblast. Nejlepším způsobem, jak to provést, je **plně nasadit vaši aplikaci logiky lokálně na svém předplatném Azure na portálu a ověřit, jestli funguje** ještě před jejich zápisem.

- **Maximální počet souběžných testovacích jednotek** -  *[povinné pole]* počet instancí testovacích jednotek, které jsou už nasazené a čekají na přístup pro vybranou oblast. Zákazníci můžou k těmto testovacím jednotkám okamžitě přistupovat, ale nemusíte čekat na nasazení.

    *Poznámka:* Pokud používáte webinář/Class, kde chcete, aby celý počet studentů v rámci testovacích jednotek byl stejný, doporučujeme publikovat s N počtem aktivních instancí a poté, co je třída nadlimitní, aby se znovu publikovala na běžný počet aktivních instancí.

- **Doba trvání testovacích jednotek (hodiny) –** *[povinné pole]* doba trvání, jak dlouho bude testovací jednotka aktivní, v \# hodin. Testovací jednotka se po skončení tohoto časového období ukončí automaticky.

- **Název skupiny prostředků Azure –** *[povinné pole]* zapište do názvu skupiny prostředků, kde se budou ukládat testovací jednotky aplikace logiky.

- **Přiřaďte název aplikace logiky –** *[povinné pole]* zapište do aplikace logiky, která se používá k přiřazení uživatele na testovací jednotce předtím, než ho zákazník dostane. napište sem název této aplikace logiky. Ujistěte se, že je tento soubor uložený ve skupině prostředků výše.

- **Zrušení zřízení názvu aplikace logiky –** *[povinné pole]* napište název aplikace logiky pro zrušení zřízení všech prostředků vytvořených na testovacím disku. Ujistěte se, že je tento soubor uložený ve skupině prostředků výše.

- **Přístup k informacím –** *[povinné pole]* poté, co zákazník získá testovací verzi, zobrazí se jim informace o přístupu. Tyto pokyny jsou určeny pro sdílení užitečných výstupních parametrů z testovacích jednotek Správce prostředků šabloně. Chcete-li zahrnout výstupní parametry, použijte dvojité složené závorky (například **{{outputName}}** ) a budou vloženy do umístění správně. (Pro vykreslení v front-endu se doporučuje formátování řetězce HTML.)

### <a name="test-drive-deployment-subscription-details"></a>Podrobnosti o předplatném nasazení testovacích jednotek

Poslední část, která se má vyplnit, je umožnit automatické nasazení testovacích jednotek tím, že se připojíte k předplatnému Azure a Azure Active Directory (AD).

![Podrobnosti o předplatném nasazení testovacích jednotek](./media/azure-resource-manager-test-drive/subdetails1.png)

**ID předplatného Azure** *[povinné pole]* uděluje přístup ke službám Azure a Azure Portal. Předplatné je místo, kde se oznamuje využití prostředků, a účtují se služby. Pokud ještě nemáte **samostatné** předplatné Azure pro testovací disky, pokračujte prosím a udělejte si ho. ID předplatných Azure můžete najít tak, že se přihlásíte Azure Portal a v nabídce na levé straně přejdete k předplatným.
(Příklad: "a83645ac-1234-5ab6-6789-1h234g764ghty")

![Předplatná Azure](./media/azure-resource-manager-test-drive/subdetails2.png)

**ID tenanta Azure AD** *[povinné pole]* Pokud máte ID tenanta, které už máte k dispozici, najdete ho níže v části vlastnosti-\> ID adresáře.

![Azure Active Directory](./media/azure-resource-manager-test-drive/subdetails3.png)

V opačném případě vytvořte nového tenanta v Azure Active Directory.

![Obrazovka vlastností Azure Active Directory](./media/azure-resource-manager-test-drive/subdetails4.png)

! Azure Active Directory] (./Media/Azure-Resource-Manager-Test-Drive/subdetails5.png)

![Azure Active Directory tenantů](./media/azure-resource-manager-test-drive/subdetails6.png)

**Aplikace Azure AD ID** *[povinné pole]* další krok je vytvoření a registrace nové aplikace. Tuto aplikaci použijeme k provádění operací s instancí testovacích jednotek.

1. Přejděte do nově vytvořeného adresáře nebo už existujícího adresáře a v podokně filtru vyberte Azure Active Directory.
2. Vyhledejte "Registrace aplikací" a klikněte na Přidat.
3. Zadejte název aplikace.
4. Vyberte typ "webová aplikace/rozhraní API".
5. Zadejte libovolnou hodnotu v přihlašovací adrese URL, kterou jsme vyhráli\'t toto pole používáme.
6. Klikněte na vytvořit.
7. Po vytvoření aplikace přejděte do části vlastnosti –\> nastavit aplikaci jako více tenantů a klikněte na Uložit.

Klikněte na Uložit. Posledním krokem je vyvýšení ID aplikace pro tuto registrovanou aplikaci a její vložení do pole testovací jednotka.

![ID aplikace Azure Active Directory](./media/azure-resource-manager-test-drive/subdetails7.png)

V případě, že aplikaci používáme k nasazení do předplatného, musíme aplikaci přidat jako přispěvatele v předplatném. Pokyny k těmto akcím jsou uvedené níže:

1. Přejděte do okna předplatná a vyberte příslušné předplatné, které používáte pouze pro testovací jednotku.
1. Klikněte na **Řízení přístupu (IAM)** .
1. Klikněte na kartu **přiřazení rolí** .  ![Azure Active Directory, přidání nového Access Control hlavního](./media/azure-resource-manager-test-drive/SetupSub7_1.jpg)
1. Klikněte na **Přidat přiřazení role**.
1. Nastavte roli jako **Přispěvatel**.
1. Zadejte název aplikace Azure AD a vyberte aplikaci, pro kterou chcete roli přiřadit.
    oprávnění Azure Active Directory ![](./media/azure-resource-manager-test-drive/SetupSub7_2.jpg)
1. Klikněte na **Uložit**.

**Aplikace Azure AD Key –** *[povinné pole]* konečné pole vygeneruje ověřovací klíč. V části klíče přidejte popis klíče, nastavte dobu trvání na bez vypršení platnosti a pak vyberte Uložit. Je **důležité** , abyste se vyhnuli vypršení platnosti klíče, což způsobí přerušení testovacích jednotek v produkčním prostředí. Zkopírujte tuto hodnotu a vložte ji do pole požadované testovací jednotky.

![Oddíl Azure Active Directory klíčů](./media/azure-resource-manager-test-drive/subdetails8.png)

> [!CAUTION]
> Nemůžete použít Azure App Registration Preview, protože aktuálně negeneruje klíč kódovaný v kódování Base64.


<a name="next-steps"></a>Další kroky
----------

Teď, když máte vyplněná všechna pole testovacích jednotek, Projděte a **znovu publikujte** vaši nabídku. Po úspěšném dokončení testovacího procesu byste si měli projít rozsáhlé prostředí pro zákazníky ve **verzi Preview** vaší nabídky. Spusťte testovací jednotku v uživatelském rozhraní a ověřte, zda jsou vaše testovací jednotky správně nasazeny.

Je důležité si uvědomit, že neodstraníte žádnou část testovacího disku, protože jsou zřízena pro vaše zákazníky, takže služba Test Drive po dokončení práce s ním automaticky vyčistí tyto skupiny prostředků.

Jakmile budete mít v nabídce Preview pohodlí, teď je to čas do **Live**! Po publikování nabídky od Microsoftu probíhá finální proces kontroly, který vám umožní dvakrát zkontrolovat celý konečný zážitek. Pokud z nějakého důvodu se nabídka odmítne, pošleme oznámení technickému kontaktu pro vaši nabídku, která vysvětluje, co je potřeba k tomu, abychom mohli opravit.

Pokud máte více otázek, hledáte Rady pro řešení potíží nebo chcete, aby testovací verze byla více úspěšná, přejděte na [Nejčastější dotazy, řešení potíží & osvědčené postupy](./marketing-and-best-practices.md).
