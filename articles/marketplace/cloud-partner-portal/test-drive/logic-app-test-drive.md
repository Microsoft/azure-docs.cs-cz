---
title: Testovací cesta aplikace logiky | Azure Marketplace
description: Vysvětluje, jak vytvořit jejich Testovací protokol, který se připojuje k instanci Dynamics AX/CRM nebo jakémukoli jinému prostředku mimo azure.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: dsindona
ms.openlocfilehash: 766f893d71ca0830fe8b69c50145603c6544cc3f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80278378"
---
<a name="logic-app-test-drive"></a>Testovací jednotka aplikace logiky
====================

Tento článek je určen pro vydavatele, kteří mají svou nabídku na AppSource a chtějí vytvořit svou testovací disku, která se připojí k instanci Dynamics AX/CRM nebo jinému prostředku mimo azure.

<a name="how-to-build-a-logic-app-test-drive"></a>Jak vytvořit testovací jednotku aplikace logiky
-----------------------------------

Dokumentace k testovacím disku pro testovací jednotky aplikace logiky je momentálně stále na [GitHubu](https://github.com/Microsoft/AppSource/blob/master/Setup-your-Azure-subscription-for-Dynamics365-Operations-Test-Drives.md) for Operations and [Customer Engagement](https://github.com/Microsoft/AppSource/wiki/Setting-up-Test-Drives-for-Dynamics-365-app), přejděte si k tomu další informace.

<a name="how-to-publish-a-test-drive"></a>Jak publikovat testovací jízdu
---------------------------

Teď, když máte vytvořenou testovací jízdu, tato část provede každé z polí, které jsou pro úspěšné publikování testovací jednotky nutné.

![Povolení funkce testovací jednotky](./media/azure-resource-manager-test-drive/howtopub1.png)

První a nejdůležitější pole je přepnout, zda chcete Test formuláře se všemi požadovanými poli jsou uvedeny pro vás vyplnit. Když vyberete **Ne,** formulář se zakáže a pokud znovu publikujete se zakázanou testovací jednotkou, testovací jednotka se odebere z produkčního prostředí.

*Poznámka:* Pokud existují testovací jednotky aktivně používané uživateli, tyto testovací jednotky budou nadále spuštěny, dokud jejich relace nevyprší.

### <a name="details"></a>Podrobnosti

Další část, kterou chcete vyplnit, jsou podrobnosti o nabídce testovací houšti.

![Podrobnosti o testovacím ovladači](./media/azure-resource-manager-test-drive/howtopub2.png)

**Popis -** *[Povinné pole]* Toto je místo, kde napíšete hlavní popis o tom, co je na testovací jízdě. Zákazník si sem přijde přečíst, jaké scénáře bude vaše testovací jízda o vašem produktu zahrnovat. 

**Uživatelská příručka -** *[Povinné pole]* Toto je podrobný návod k testovací jízdě. Zákazník to otevře a může projít přesně to, co chcete, aby dělali po celou dobu jejich testovací jízdy. Je důležité, aby tento obsah byl snadno pochopitelný a sledovat! (Musí se jednat o soubor PDF)

**Test Drive Demo Video -** \[Doporučeno\] Podobně jako v uživatelské příručce, je nejlepší zahrnout instruktážní video o vašem zážitku z testovací jízdy. Zákazník bude sledovat to před nebo během jejich Zkušební jízdy a může projít přesně to, co chcete, aby dělali v průběhu jejich Zkušební jízdy. Je důležité, aby tento obsah byl snadno pochopitelný a sledovat!

- **Název** – název videa
- **Odkaz** - Musí být vložené URL z YouTube nebo Vimeo. Příklad, jak získat vloženou adresu URL, je následující:
- **Miniatura** – musí se jednat o obraz ve vysoké kvalitě (533 × 324) pixelů. Doporučujeme, abyste zde pořídili snímek obrazovky s některými částmi prostředí testování.

Níže je uvedeno, jak se tato pole zobrazí pro zákazníka během jeho zkušeností s testovací mitou.

![Testovací jednotka pole vypadat a cítit](./media/azure-resource-manager-test-drive/howtopub4.png)

### <a name="technical-configuration"></a>Technická konfigurace

Další část, kterou chcete vyplnit, je, kde nakonfigurujete aplikaci logiky testovací jednotky a definujete, jak konkrétně vaše instance testovací jednotky fungují.

![Technická konfigurace zkušební jízdy](./media/azure-resource-manager-test-drive/howtopub5_logicapp.png)

- **Oblast** - *[Povinné pole]* Oblast, kterou vyberete, je místo, kde vyberete, kde se nasadí prostředky aplikace logiky testovací jednotky.

    *Poznámka:* Pokud vaše aplikace logiky obsahuje vlastní prostředky, které jsou uloženy v oblasti, ujistěte se, že oblast je vybrána zde. Nejlepší způsob, jak to udělat, je **plně nasadit aplikaci logiky místně na vašem předplatném Azure na portálu a ověřit, že funguje** před zápisem tady.

- **Maximální souběžné testovací jednotky** - *[Povinné pole]* Počet instancí testovací jednotky, které jsou již nasazeny a čekají na přístup pro vybranou oblast. Zákazníci mohou okamžitě přistupovat k této testovací jednotky, spíše než muset čekat na nasazení.

    *Poznámka:* Pokud používáte webinář/třídu, kde chcete, aby všichni vaši N počet studentů, aby test drive, je doporučeno publikovat s N počet Hot instance a pak po dokončení třídy znovu publikovat zpět na normální počet Hot instance.

- **Doba trvání testovací jednotky (hodiny) -** *[Povinné pole]* Doba trvání, po kterou bude testovací jízda aktivní, v \# hodinách. Testovací cesta se automaticky ukončí po uplynutí tohoto časového období.

- **Název skupiny prostředků Azure –** *[povinné pole]* Zapište v názvu skupiny prostředků, kde se uloží testovací jednotky aplikace logiky.

- **Přiřadit název aplikace logiky -** *[povinné pole]* Zápis v aplikaci logiky, který se používá k přiřazení uživatele v testovací jízdě dříve, než ji zákazník získá, napište název této aplikace logiky zde. Zkontrolujte, zda je tento soubor uložen ve výše uvedené skupině prostředků.

- **Zrušení zřízení název aplikace logiky -** *[Povinné pole]* Zápis v názvu aplikace logiky pro zrušení zřízení všech prostředků vytvořených v testovací jednotce. Zkontrolujte, zda je tento soubor uložen ve výše uvedené skupině prostředků.

- **Přístup k informacím –** *[Povinné pole]* Poté, co zákazník získá testovací jízdu, jsou jim předloženy přístupové informace. Tyto pokyny jsou určeny ke sdílení užitečných výstupních parametrů ze šablony Správce prostředků testovací jednotky. Chcete-li zahrnout výstupní parametry, použijte dvojité složené závorky (například **{{outputname}}** a budou vloženy správně do umístění. (Zde se doporučuje formátování řetězce HTML, které se vykresluje v přední části).

### <a name="test-drive-deployment-subscription-details"></a>Podrobnosti o předplatném nasazení testovací jednotky

Poslední část, kterou chcete vyplnit, je možnost nasadit testovací jednotky automaticky připojením předplatného Azure a služby Azure Active Directory (AD).

![Podrobnosti o předplatném nasazení testovací jednotky](./media/azure-resource-manager-test-drive/subdetails1.png)

**ID předplatného Azure** *[povinné pole]* To uděluje přístup ke službám Azure a portálu Azure. Předplatné je místo, kde se účtuje využití prostředků a služby se účtují. Pokud ještě nemáte **samostatné** předplatné Azure pro testovací jednotky, pokračujte a vytvořte si ho. Id předplatného Azure najdete tak, že se přihlásíte na portál Azure a přejdete na předplatná v nabídce na levé straně.
(Příklad: "a83645ac-1234-5ab6-6789-1h234g764ghty")

![Předplatná Azure](./media/azure-resource-manager-test-drive/subdetails2.png)

**ID klienta Azure AD** *[Povinné pole]* Pokud máte ID klienta,\> které je už k dispozici, najdete ho níže v ID služby Vlastnosti – adresář.

![Azure Active Directory](./media/azure-resource-manager-test-drive/subdetails3.png)

V opačném případě vytvořte nového klienta ve službě Azure Active Directory.

![Obrazovka vlastností služby Azure Active Directory](./media/azure-resource-manager-test-drive/subdetails4.png)

! Azure Active Directory](./media/azure-resource-manager-test-drive/subdetails5.png)

![Klienti Služby Azure Active Directory](./media/azure-resource-manager-test-drive/subdetails6.png)

**Azure AD App ID** *[Povinné pole]* Dalším krokem je vytvoření a registrace nové aplikace. Tuto aplikaci použijeme k provádění operací s instancí testovací jednotky.

1. Přejděte do nově vytvořeného adresáře nebo již existujícího adresáře a v podokně filtrů vyberte adresář Azure Active Directory.
2. Hledat "Registrace aplikací" a klikněte na "Přidat"
3. Zadejte název aplikace.
4. Vyberte typ jako "Webová aplikace / ROZHRANÍ API"
5. V přihlašovací adrese URL uveďte\'libovolnou hodnotu, toto pole nebudeme používat.
6. Klikněte na vytvořit.
7. Po vytvoření aplikace přejděte na\> Vlastnosti – Nastavte aplikaci jako víceklientské a stiskněte tlačítko Uložit.

Klikněte na Uložit. Posledním krokem je uchopit ID aplikace pro tuto registrovanou aplikaci a vložit ji do pole Test Drive zde.

![ID aplikace Azure Active Directory](./media/azure-resource-manager-test-drive/subdetails7.png)

Vzhledem k tomu, že používáme aplikaci k nasazení do předplatného, musíme přidat aplikaci jako přispěvatele na předplatné. Pokyny pro tyto jsou následující:

1. Přejděte do okna Předplatná a vyberte příslušné předplatné, které používáte pouze pro testovací verzi.
1. Klikněte na **Řízení přístupu (IAM)**.
1. Klikněte na kartu **Přiřazení rolí.**  ![Azure Active Directory, přidání nového hlavního povinného řízení přístupu](./media/azure-resource-manager-test-drive/SetupSub7_1.jpg)
1. Klepněte na tlačítko **Přidat přiřazení role**.
1. Nastavte roli **přispěvatele**.
1. Zadejte název aplikace Azure AD a vyberte aplikaci, která roli přiřadí.
    ![Oprávnění služby Azure Active Directory](./media/azure-resource-manager-test-drive/SetupSub7_2.jpg)
1. Klikněte na **Uložit**.

**Klíč aplikace Azure AD –** *[Povinné pole]* Konečné pole je generovat ověřovací klíč. V části klíče přidejte popis klíče, nastavte dobu trvání na nikdy nevyprší a vyberte uložit. Je **důležité,** aby se zabránilo mít vypršela klíč, který zlomí zkušební jízdu v produkčním prostředí. Zkopírujte tuto hodnotu a vložte ji do požadovaného pole Testovací jednotka.

![Oddíl Klíče služby Azure Active Directory](./media/azure-resource-manager-test-drive/subdetails8.png)

> [!CAUTION]
> Azure App Registration Preview nelze použít, protože aktuálně negeneruje kódovaný klíč base64.


<a name="next-steps"></a>Další kroky
----------

Teď, když máte vyplněná všechna pole testovací jednotky, projděte a **znovu publikujte** svou nabídku. Jakmile vaše testovací verze projde certifikačním procesem, měli byste v **náhledu** nabídky projít rozsáhlým testem zkušeností zákazníků. Spusťte testovací jednotku v ui a ověřte, že vaše testovací jednotky jsou plně nasazeny správně.

Je důležité si uvědomit, že neodstraníte žádnou část testovací jednotky, protože jsou zřízeny pro vaše zákazníky, takže služba Test Drive tyto skupiny prostředků automaticky vyčistí po dokončení zákazníka.

Jakmile se budete cítit pohodlně s nabídkou Náhled, nyní je čas **jít žít!** Po publikování nabídky je proveden proces závěrečné kontroly od společnosti Microsoft, aby bylo možné zkontrolovat celou dobu od začátku do konce. Pokud z nějakého důvodu bude nabídka odmítnuta, zašleme technickému kontaktu pro vaši nabídku oznámení, ve kterém vysvětlíme, co bude třeba opravit.

Máte-li další dotazy, hledáte rady pro řešení potíží nebo chcete, aby byla vaše testovací jízda úspěšnější, přejděte na [nejčastější dotazy, řešení potíží & doporučené postupy](./marketing-and-best-practices.md).
