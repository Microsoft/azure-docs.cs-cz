---
title: Smlouvy k Azure EA a jejich změny
description: Tento článek vysvětluje, jak smlouvy k Azure EA a jejich změny ovlivňují vaše používání portálu Azure EA.
author: bandersmsft
ms.author: banders
ms.date: 05/01/2020
ms.topic: conceptual
ms.service: cost-management-billing
ms.reviewer: boalcsva
ms.openlocfilehash: 570a7b3bef702d0e42e23bc4b7af41cc431b9cc8
ms.sourcegitcommit: 366e95d58d5311ca4b62e6d0b2b47549e06a0d6d
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/01/2020
ms.locfileid: "82690996"
---
# <a name="azure-ea-agreements-and-amendments"></a>Smlouvy k Azure EA a jejich změny

Tento článek popisuje, jak mohou smlouvy Azure EA a jejich změny ovlivnit váš přístup ke službám Azure, jejich používání a úhrady za ně.

## <a name="amendment-status"></a>Stav změny

Veškeré změny u zákazníků Azure musí projít přes jejich partnera nebo tým, který se stará o účty Microsoft, a musí je zpracovat regionální operační centrum. Pokud se domníváte, že se změna nezpracovala, obraťte se na svého partnera, softwarového poradce nebo tým, který se stará o účty Microsoft.

## <a name="enrollment-provisioning-status"></a>Stav zřízení registrace

Počáteční datum nového peněžního závazku vyplývá z data, kdy ho regionální operační centrum zpracovalo. Vzhledem k tomu, že objednávky peněžních závazků prostřednictvím webu Azure EA Portal se zpracovávají v časovém pásmu UTC, může dojít ke zpoždění v případě, že nákupní objednávka peněžního závazku byla zpracována v jiné oblasti. Začátek peněžního závazku uvádí počáteční datum pokrytí v nákupní objednávce na webu https://www.explore.ms. Počáteční datum pokrytí představuje den, kdy se peněžní závazek zobrazí na portálu Azure EA.

## <a name="support-offer-not-provisioned"></a>Nezřízení nabídky podpory

Podporu Standard nebo Pro-Direct si můžete objednat zakoupením specifických skladových položek (SKU) představujících podporu. Objednávka se podobá nákupní objednávce položky SKU představující peněžní položku v rámci smlouvy EA. Například 6QK-00001. Čísla položek SKU představujících podporu jsou W6T-00002 (Pro-Direct) a W6T-00003 (Standard). Než si tyto jednotky SKU zakoupíte, projděte si aktuální nabídky podpory.

Registrace musí zahrnovat alespoň jeden aktivní účet, aby bylo možné nabídku podpory zřídit.

 Pro některé zákazníky je k dispozici [nabídka plánu podpory pro smlouvu Azure Enterprise](https://azure.microsoft.com/offers/enterprise-agreement-support/).

## <a name="enrollment-status"></a>Stav registrace

Registrace má jednu z následujících hodnot stavu. Každá hodnota určuje, jak můžete registraci používat a jak k ní můžete získat přístup. Stav registrace určuje, v jaké fázi se vaše registrace nachází. Poznáte z něho, jestli je registraci nutné před použitím aktivovat. Případně že vypršela platnost počátečního období a za nadlimitní využití se vám účtují poplatky.

**Čeká na vyřízení:** Správce registrace se musí přihlásit k portálu Azure EA. Po přihlášení se registrace přepne do stavu **Aktivní**.

**Aktivní:** Registrace je přístupná a použitelná. Na portálu Azure EA můžete vytvářet účty a předplatná. Registrace zůstane aktivní až do koncového data smlouvy Enterprise.

**Neurčené období prodloužení:** Tento stav vznikne po dosažení koncového data smlouvy Enterprise. Než registrace EA dosáhne koncového data smlouvy Enterprise, měl by se správce registrace rozhodnout pro jednu z těchto variant:

- Prodloužení registrace přidáním dalšího peněžního závazku
- Přesun existující registrace do nové registrace
- Migrace do programu Microsoft Online Subscription (MOSP)
- Potvrzení deaktivace všech služeb přidružených k registraci

**Platnost vypršela:** Když registrace EA dosáhne koncového data smlouvy Enterprise, její platnost vyprší. Zákazník EA je výslovně vyřazen z období prodloužení a všechny jeho služby se deaktivují.

Od 1. srpna 2019 nepřijímáme žádné další žádosti o výslovné vyřazení u komerčních zákazníků Azure. Místo toho budou všechny registrace přecházet do neurčeného období prodloužení. Pokud chcete služby Azure přestat používat, uzavřete vaše předplatné na [webu Azure EA Portal](https://portal.azure.com). Nebo může žádost o ukončení podat partner na adrese https://www.explore.ms. U zákazníků s typy smluv pro státní instituce se nic nemění.

**Přesunuto:** Stav Přesunuto se použije u registrací, jejichž přidružené účty a služby byly přesunuty do nové registrace. Registrace se nepřesouvají automaticky, pokud se při prodloužení vygeneruje nové číslo registrace. Aby mohl proběhnout automatický přenos, je třeba do žádosti zákazníka o prodloužení zahrnout předchozí číslo registrace.

## <a name="partner-markup"></a>Marže u partnera

Díky cenovým maržím u partnerů na portálu Azure EA je možné lépe vykazovat náklady pro zákazníky. Portál Azure EA uvádí využití a ceny nakonfigurované partnery pro jejich zákazníky.

Partneři zajišťující správu si u nepřímých smluv Enterprise navyšují cenu o určitou procentuální přirážku. Procentuální přirážka se uplatní u všech sazeb za služby poskytované Microsoftem na webu Azure EA Portal. Jde například o sazby u měřičů, peněžní závazky a objednávky. Když partner publikuje přirážku, zákazník uvidí náklady spojené s Azure na webu Azure EA Portal. Například v souhrnu využití, na cenících a na stažených sestavách využití.

Od září 2019 mohou partneři uplatňovat marži kdykoli během období. Nemusí tedy čekat do příštího výročí smlouvy.

Microsoft nezpřístupní a nevyužije poskytnutou přirážku a související ceny k žádnému účelu, pokud ho k tomu distribuční partner explicitně neoprávní.

### <a name="how-the-calculation-works"></a>Jak funguje výpočet

Poskytovatel LSP poskytne jednu procentní hodnotu na webu EA Portal.  Všechny komerční informace na portálu se zvýší o počet procent, který tento poskytovatel LSP uvedl. Příklad:

- Zákazník podepíše smlouvu EA s peněžním závazkem ve výši 100 000 USD.
- Sazba měřiče za službu A je 10 USD za hodinu.
- LSP nastaví na webu EA Portal procentní přirážku ve výši 10 %.
- Příklad uvedený níž ukazuje, jak se budou zákazníkům zobrazovat komerční informace:
    - Peněžní zůstatek: 110 000 USD
    - Sazba měřiče pro službu A: 11 USD za hodinu.
    - Informace o využití/hostingu pro službu A, pokud se využívá 100 hodin: 1 100 USD
    - Peněžní zůstatek, který má zákazník k dispozici po odečtení využití služby A: 108 900 USD

### <a name="when-to-use-a-markup"></a>Kdy používat přirážku

Tuto funkci použijte, pokud nastavujete stejnou procentní přirážku pro VŠECHNY komerční transakce v rámci smlouvy EA. To znamená, pokud přirážku používáte pro informace o peněžních závazcích, sazby měřičů, informace o objednávkách atd.

V následujících případech funkci přirážky nepoužívejte:
- Používáte jiné sazby pro peněžní závazek a sazby měřičů.
- Používáte různé sazby pro různé měřiče.

Pokud používáte různé sazby pro různé měřiče, doporučujeme vytvořit vlastní řešení na základě klíče rozhraní API, který může zákazník poskytnout a na jeho základě si vyžádat data týkající se využití a zajišťovat sestavy.

### <a name="other-important-information"></a>Další důležité informace

Cílem této funkce je poskytnout cílovému zákazníkovi odhad nákladů na Azure. Za všechny finanční transakce v rámci smlouvy EA zodpovídá poskytovatel LSP.

Před publikováním cen s přirážkami pro koncového zákazníka prosím nezapomeňte zkontrolovat komerční informace – informace o peněžním zůstatku, ceník atd.

### <a name="how-to-add-a-price-markup"></a>Jak přidat cenovou přirážku

**Krok 1: Přidání cenové přirážky**

1. V levém navigačním panelu portálu Enterprise Portal klikněte na **Sestavy**.
1. V části _Usage Summary_ (Souhrn využití) klikněte na modře označené slovo **Markup** (Přirážka).
1. Zadejte procento přirážky (mezi -100 a 100) a potom klikněte na **Preview** (Zobrazit náhled).


**Krok 2: Kontrola a ověření**

V zobrazení zákazníka v části _Usage Summary_ (Souhrn využití) zkontrolujte cenu s přirážkou pro období závazku. V zobrazení partnera bude stále dostupná cena Microsoftu. Tato zobrazení je pro přirážku u partnera možné přepínat s využitím přepínače „lidé“ v pravé horní části.

1. Zkontrolujte ceny v ceníku.
1. Změny je možné provést před publikováním, a to volbou **Edit** (Úpravy) na kartě _View Usage Summary > Customer View_ (Zobrazit souhrn využití > Zobrazení zákazníka).  
  Pro ceny služeb i zůstatky závazků se použije stejná procentní přirážka. Pokud máte jiný počet procent pro peněžní zůstatek a sazby měřičů nebo různé počty procent pro různé služby, tuto funkci prosím nepoužívejte.

**Krok 3: Publikování**

Po kontrole a ověření cen klikněte na **Publish** (Publikovat).
  
Ceny s přirážkou budou podnikovým správcům k dispozici hned po výběru této možnosti. Přirážku nejde měnit. Musíte ji deaktivovat a znovu začít krokem 1.

### <a name="which-enrollments-have-a-markup-enabled"></a>U kterých prováděcích smluv je přirážka aktivovaná?

Pokud chcete zkontrolovat, jestli je publikovaná přirážka, v levé navigační nabídce klikněte na **Manage** (Spravovat) a potom klikněte na kartu **Enrollment** (Registrace). Zaškrtněte políčko pro registraci a podívejte se na stav přirážky v části _Enrollment Detail_ (Podrobnosti o registraci). Aktuální stav funkce přirážky pro příslušnou smlouvu EA se zobrazí jako Disabled (Neaktivní), Preview nebo Published (Publikovaná).

### <a name="how-can-the-customer-download-usage-estimates"></a>Jak si může zákazník stáhnout odhady využití?

Jakmile se publikuje přirážka partnera, bude mít nepřímý zákazník přístup k měsíčním souborům .csv se zůstatkem a poplatky a k souborům .csv s podrobnými informacemi o využití. Soubory s podrobnými informacemi o využití budou také zahrnovat sazby za prostředek a rozšířené náklady.

### <a name="how-can-i-as-partner-apply-markup-to-existing-ea-customers-that-was-earlier-with-another-partner"></a>Jak mohu jako partner využít přirážku pro stávající zákazníky EA, kteří dřív byli u jiného partnera?
Partneři mohou funkci přirážky (pro Azure EA) využít po zpracování změny distribučního partnera. Není tedy nutné čekat až na další období.


## <a name="resource-commitment-and-requesting-quota-increases"></a>Závazek týkající se prostředků a žádost o navýšení kvóty

**Systém pro každé předplatné vynucuje následující výchozí kvóty:**

| **Prostředek** | **Výchozí kvóta** | **Komentáře** |
| --- | --- | --- |
| Instance výpočtu Microsoft Azure | 20 souběžných malých výpočetních instancí nebo jejich ekvivalent v jiných velikostech výpočetních instancí | Následující tabulka uvádí, jak vypočítat ekvivalentní počet malých instancí:<ul><li> Velmi malá – 1 ekvivalentní malá instance </li><li> Malá – 1 ekvivalentní malá instance </li><li> Střední – 2 ekvivalentní malé instance </li><li> Velká – 4 ekvivalentní malé instance </li><li> Velmi velká – 8 ekvivalentních malých instancí </li> </ul>|
| Virtuální počítače s instancemi výpočtu Microsoft Azure v2 | EA: 350 jader | Virtuální počítače GA IaaS v2:<ul><li> Řada A0\_A7 – 350 jader </li><li> Řada B\_A0\_A4 – 350 jader </li><li> Řada A8\_A9 – 350 jader </li><li> Řada DF – 350 jader</li><li> GF – 350 jader </li></ul>|
| Hostované služby Microsoft Azure | 6 hostovaných služeb | Tento limit hostovaných služeb nelze pro jednotlivé předplatné navýšit na více než šest. Pokud požadujete další hostované služby, přidejte prosím další předplatná. |
| Microsoft Azure Storage | 5 účtů úložiště, každý s maximální velikostí 100 TB | Počet účtů úložiště můžete navýšit až na 20 na jedno předplatné. Pokud požadujete další účty úložiště, přidejte prosím další předplatná. |
| SQL Azure | 149 databází jednoho z typů (tj. Web Edition nebo Business Edition) |   |
| Řízení přístupu | 50 oborů názvů na účet 100 milionů transakcí řízení přístupu za měsíc |   |
| Service Bus | 50 oborů názvů na účet 40 připojení služby Service Bus | Kvóty zákazníků, kteří si kupují připojení služby Service Bus prostřednictvím balíčků připojení, se budou rovnat střednímu bodu mezi zakoupeným balíčkem připojení a dalším vyšším balíčkem připojení. Zákazníci, kteří si zvolí balíček 500 připojení, budou mít kvótu 750. |

## <a name="resource-commitment"></a>Závazek týkající se prostředků

Microsoft poskytne služby až do úrovně využití, která je součástí zakoupeného měsíčního závazku (závazek poskytovat služby). Jakékoliv zvýšení úrovní využívání prostředků služeb (například zvýšení počtu běžících výpočetních instancí nebo zvýšení kapacity používaného úložiště) závisí na jejich dostupnosti.

Žádná výše uvedená kvóta není závazkem poskytovat služby. Počet simultánně běžících malých výpočetních instancí (nebo odpovídajícího ekvivalentu), které Microsoft poskytne jako součást závazku poskytovat služby, se určí jako podíl počtu hodin vyhrazených malých výpočetních instancí zakoupených v daném měsíci a počtu hodin nejkratšího měsíce v roce (tj. 672 únorových hodin).

## <a name="requesting-a-quota-increase"></a>Žádost o navýšení kvóty

O navýšení kvóty můžete požádat kdykoli, a to odesláním [online žádosti](https://g.microsoftonline.com/0WAEP00en/6). Pro zpracování žádosti zadejte následující informace:

- Účet Microsoft nebo pracovní nebo školní účet přidružený k vlastníkovi účtu vašeho předplatného. Tato e-mailová adresa se používá pro přihlášení k portálu Microsoft Azure a správě vašich předplatných. Ujistěte se také, že tento účet je přidružený k registraci EA.
- Prostředky a objemy, pro které vyžadujete zvýšení kvóty.
- ID předplatného vývojářského portálu Azure přidruženého k vaší službě.
  - Pokud potřebujete informace o tom, jak zjistit ID předplatného, [kontaktujte prosím podporu](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview).

## <a name="azure-compute-pre-purchase-plan-cpp"></a>Plán předběžného nákupu pro výpočty Azure (CPP)

Plán předběžného nákupu pro výpočty Azure (CPP – dříve označovaný jako Azure Compute Promo) je nabídka, která zákazníkům se stabilními a předvídatelnými úlohami poskytuje příležitost získat výpočetní funkce s výraznou slevou, a to předplacením výpočtů Azure na dobu 12 měsíců. Je k dispozici pro EA a zobrazuje se na webu EA Portal.

Pokud jste si objednali skladovou položku CPP, je určená pro konkrétní typ počítače v konkrétním datacentru. V současnosti v rámci této funkce neposkytujeme seznam všech nákupů CPP, které zákazník realizoval, takže neexistuje seznam nevyužitých kreditů. Pokud si ale zakoupené typy počítačů nakonfigurujete v datacentru, ve kterém byly zakoupené, bude se vám na webu EA Portal zobrazovat měsíční přidělení v sestavě souhrnu využití.

Předplacená částka se bude zobrazovat jako kredit ve sloupci Zahrnuté jednotky a za využití příslušných funkcí se nebudou účtovat žádné poplatky, dokud se tyto zahrnuté jednotky nespotřebují. Jakmile se spotřebují, bude se další využití účtovat vyjednanými sazbami v běžném ceníku pro zákazníky EA.

Způsob nákupu kreditů vychází z počtu počítačů konkrétní třídy za měsíc. Příklad: Pokud jste si koupili 70 virtuálních počítačů A2 na roky v oblasti USA – východ 2, nebude se tato skutečnost na webu EA Portal zobrazovat stejným způsobem jako při nákupu.

Měsíční počítače se počítají tak, jako kdyby běžely 744 hodin za měsíc. Převod této doby na hodiny je 70 krát 744 neboli 52 080 zakoupených hodin virtuálních počítačů A2 za měsíc. Vzhledem k tomu, že na webu EA Portal se v souhrnu využití virtuální počítače A2 uvádějí v přírůstcích po 100 hodinách, uvidíte kredit 520,8 (52 080/100) zahrnutých jednotek za měsíc a také se vám zobrazí jednotky, které jste v aktuálním měsíci už spotřebovali. Poplatky za využití se budou zobrazovat jako nula, dokud spotřebované jednotky za měsíc nepřekročí měsíční zahrnutý počet jednotek.

Různé typy počítačů používají různé měrné jednotky. Například virtuální počítače D3 a D4 v sestavě souhrnu využití jako měrnou jednotku používají 10 hodin, takže nákup 70 takových počítačů by představoval 5 208 zahrnutých jednotek za měsíc (52080/10).

Následující vzorec uvádí počet zakoupených virtuálních počítačů na základě jejich měrné jednotky:

<center><b> (zahrnuté množství × měrná jednotka)/744 </b></center>

## <a name="plan-skus"></a>Skladové položky plánu

Skladové položky plánu nabízejí možnost zakoupit si sadu integrovaných služeb společně se zvýhodněnou sazbou. Skladové jednotky plánu jsou navržené tak, aby se vzájemně doplňovaly prostřednictvím dalších integrovaných nabídek a slouží k větší úspoře nákladů.

Jedním z příkladů je předplatné OMS (Operations Management Suite). OMS nabízí jednoduchý způsob, jak získat přístup k úplné sadě cloudových funkcí správy, včetně analýz, konfigurace, automatizace, zabezpečení, zálohování a zotavení po havárii. Předplatná OMS zahrnují práva k součástem System Center, aby poskytovala kompletní řešení pro hybridní cloudová prostředí.

Podnikoví správci můžou přiřazovat vlastníky účtů, aby zřizovali dříve zakoupené skladové položky plánu na portálu Enterprise Portal pomocí následujících kroků:

### <a name="view-the-price-sheet-to-check-included-quantity"></a>Zobrazení ceníku kvůli kontrole zahrnutého množství

1. Přihlaste se jako podnikový správce.
1. V levém navigačním panelu klikněte na **Sestavy**.
1. Klikněte na kartu **Ceník**.
1. Klikněte na ikonu Stáhnout v pravém horním rohu.
1. Vyhledejte odpovídající výrobní čísla skladových položek plánu pomocí filtru pro sloupec Zahrnuté množství a vyberte hodnoty větší než 0.

### <a name="provision-the-plan-skus"></a>Zřízení skladových položek plánu

Podnikoví správci můžou přidat nové vlastníky účtů, přidružit stávajícího vlastníka účtu nebo požádat stávající vlastníky účtu, aby se přihlásili k portálu Azure EA Portal a zřídili dříve zakoupené skladové položky plánu na portálu Azure EA Portal pomocí následujících kroků.  

**Přidání nového vlastníka účtu (dokončeno správcem EA):**

1. V levém navigačním panelu portálu Azure EA Portal vyberte **Spravovat**.
1. Klikněte na kartu **Účet**.
1. Na stránce Účet klikněte na **+ Přidat účet**.
1. Vyberte oddělení nebo nechte jako nepřiřazené.
1. Vyberte požadovaný typ ověřování.
1. Zadejte popisný název, který chcete použít k identifikaci tohoto účtu v sestavách.
1. Zadejte e-mailovou adresu vlastníka účtu, kterou chcete přidružit k novému účtu.
1. Potvrďte e-mailovou adresu, kterou chcete mít přidruženou k novému účtu.
1. Klikněte na tlačítko **Add** (Přidat).
1. Můžete přidat další účet, a to kliknutím na **Přidat další účet** nebo na tlačítko **Přidat** v pravém dolním rohu levého panelu nástrojů.
1. Nyní se vlastník účtu může přihlásit a přidat příslušná předplatná ke zřízení skladových položek plánu.

**Přidružení stávajícího vlastníka účtu:**

1. Na portálu Enterprise Portal klikněte na **Spravovat**.
1. Klikněte na kartu **Účet**.
1. Klikněte na **+ Přidat účet**. Zadejte účet Microsoft nebo pracovní nebo školní účet přidružený ke stávajícímu účtu.
1. Potvrďte účet Microsoft nebo pracovní nebo školní účet přidružený ke stávajícímu účtu.
1. Zadejte název, který chcete použít k identifikaci tohoto účtu v sestavách.
1. Klikněte na tlačítko **Add** (Přidat).
1. Můžete znovu vybrat možnost **+ Přidat účet** a přidat další účet nebo se výběrem tlačítka **Správce** můžete vrátit na domovskou stránku.  
1. Pokud si zobrazíte stránku Účet, nově přidaný účet se zobrazí ve stavu čekání na vyřízení. Po prvním přihlášení vlastníka účtu k webu EA Portal se změní stav na aktivní.

### <a name="existingnew-account-owners-to-create-new-subscriptions"></a>Stávající a noví vlastníci účtů pro vytváření nových předplatných

**Krok 1: Přihlášení k účtu**
1. Na portálu Azure EA Portal vyberte kartu **Spravovat** a v horní nabídce přejděte na **Předplatné**.
1. Ověřte, že jste přihlášeni jako vlastník tohoto účtu.
1. Klikněte na **+ Přidat předplatné**.
1. Klikněte na **Koupit**.

Při prvním přidávání předplatného k účtu budete muset zadat své kontaktní údaje. Při přidávání příštích předplatných se vaše kontaktní údaje vyplní automaticky.

Až budete poprvé přidávat předplatné ke svému účtu, budete požádáni o přijetí smlouvy MOSA a plánu sazeb. Tyto části se NEVZTAHUJÍ na zákazníky se smlouvou Enterprise zákazníky, ale jsou v současné době nezbytné ke zřízení vašeho předplatného. Vyšší váhu má nadále verze smlouvy Microsoft Azure Enterprise platná pro vaši registraci a váš smluvní vztah se nijak nemění. Zaškrtnutím políčka potvrďte, že s těmito podmínkami souhlasíte.

**Krok 2: Aktualizace názvu předplatného**

Všechna nová předplatná se přidají s výchozím názvem předplatného Microsoft Azure Enterprise. Je důležité aktualizovat název předplatného, aby ho bylo možné odlišit od ostatních předplatných v rámci vaší smlouvy Enterprise a zajistit, že je v sestavách na podnikové úrovni rozpoznatelné.

Klikněte na **Subscriptions** (Předplatná), klikněte předplatné, které jste vytvořili, a potom klikněte na **Edit Subscription Details** (Upravit podrobnosti předplatného).

Aktualizujte název předplatného a správce služeb a potom klikněte na značku zaškrtnutí. Název předplatného se zobrazuje na sestavách a bude také názvem projektu, který je k předplatnému přidružený na vývojářském portálu.
Může trvat až 24 hodin, než se nové předplatné objeví v seznamu předplatných.

Zobrazovat a spravovat předplatná mohou jenom vlastníci účtů.

### <a name="troubleshooting"></a>Řešení potíží

**Vlastník účtu se zobrazuje ve stavu čekání na vyřízení**

Při prvním přidání nových vlastníků účtů k registraci se u nich vždy zobrazí stav Čeká na vyřízení. Jakmile obdrží úvodní aktivační e-mail, mohou se vlastníci účtu přihlásit a aktivovat svůj účet. Po dokončení aktivace se stav účtu aktualizuje z Čeká na vyřízení na Aktivní.

**Zpoplatněné využití po nákupu skladových položek plánu**

K tomuto scénáři dochází, když zákazník nasadí služby pod nesprávným číslem registrace nebo vybere nesprávné služby.

Pokud chcete ověřit, že nasazujete v rámci správné registrace, můžete prostřednictvím ceníku zkontrolovat informace o zahrnutých jednotkách. Přihlaste se jako podnikový správce, v levém navigačním panelu klikněte na **Reports** (Sestavy) a potom vyberte kartu **Price Sheet** (Ceník). Klikněte na ikonu pro stažení v pravém horním rohu, pomocí filtru pro sloupec Zahrnuté množství vyhledejte odpovídající výrobní čísla skladových položek plánu a vyberte hodnoty větší než 0.

Zkontrolujte, že se plán OMS zobrazuje v ceníku v rámci zahrnutých jednotek. Pokud vaše registrace nemá pro plán OMS žádné zahrnuté jednotky, je plán OMS pravděpodobně pod jinou registrací. Kontaktujte tým podpory Azure Enterprise Portal Support na [https://aka.ms/AzureEntSupport](https://aka.ms/AzureEntSupport).

Pokud se zahrnuté jednotky pro služby na ceníku neshodují s tím, co jste nasadili, například analyzovaná data Operational Insights úrovně Premium vs. analyzovaná data Operational Insights úrovně Standard, znamená to, že jste pravděpodobně nasadili služby, které váš plán nepokrývá. V takovém případě prosím kontaktujte tým podpory Azure Enterprise Portal Support na [https://aka.ms/AzureEntSupport](https://aka.ms/AzureEntSupport), abychom vám mohli dále pomoci.

**Zřízené služby skladových položek plánu v nesprávné registraci**

Pokud máte více registrací a nasadili jste služby s nesprávným číslem registrace, které nemá plán OMS, kontaktujte prosím tým podpory Azure Enterprise Portal Support na [https://aka.ms/AzureEntSupport](https://aka.ms/AzureEntSupport).

## <a name="next-steps"></a>Další kroky

- Pokud chcete začít používat portál Azure EA, přečtěte si, [jak začít používat portál Azure EA](ea-portal-get-started.md).
- Správci na portálu Azure EA by si měli přečíst téma [Správa portálu Azure EA](ea-portal-administration.md), kde se seznámí s běžnými úlohami správy.
