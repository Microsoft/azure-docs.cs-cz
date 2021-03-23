---
title: Smlouvy k Azure EA a jejich změny
description: Tento článek vysvětluje, jak smlouvy k Azure EA a jejich změny ovlivňují vaše používání portálu Azure EA.
author: bandersmsft
ms.author: banders
ms.date: 01/19/2021
ms.topic: conceptual
ms.service: cost-management-billing
ms.subservice: enterprise
ms.reviewer: boalcsva
ms.openlocfilehash: d03ae5062cc81b3e042ed3b714838e14929dbba2
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/22/2021
ms.locfileid: "104778215"
---
# <a name="azure-ea-agreements-and-amendments"></a>Smlouvy k Azure EA a jejich změny

Tento článek popisuje, jak mohou smlouvy Azure EA a jejich změny ovlivnit váš přístup ke službám Azure, jejich používání a úhrady za ně.

## <a name="enrollment-provisioning-status"></a>Stav zřízení registrace

Počáteční datum nového Azure Prepaymentu (dřív označovaný jako peněžní závazek) vyplývá z data, kdy ho regionální operační centrum zpracovalo. Vzhledem k tomu, že objednávky zálohy na Azure prostřednictvím portálu Azure EA na se zpracovávají v časovém pásmu UTC, může dojít ke zpoždění v případě, že se nákupní objednávka zálohy na Azure zpracovávala v jiné oblasti. Začátek zálohy na Azure uvádí počáteční datum pokrytí v nákupní objednávce. Počáteční datum pokrytí představuje den, kdy se záloha na Azure zobrazí na portálu Azure EA.

## <a name="support-for-enterprise-customers"></a>Podpora pro podnikové zákazníky

 Pro některé zákazníky je k dispozici [nabídka plánu podpory pro smlouvu Azure Enterprise](https://azure.microsoft.com/offers/enterprise-agreement-support/).

## <a name="enrollment-status"></a>Stav registrace

Registrace má jednu z následujících hodnot stavu. Každá hodnota určuje, jak můžete registraci používat a jak k ní můžete získat přístup. Stav registrace určuje, v jaké fázi se vaše registrace nachází. Poznáte z něho, jestli je registraci nutné před použitím aktivovat. Případně že vypršela platnost počátečního období a za nadlimitní využití se vám účtují poplatky.

**Čeká na vyřízení:** Správce registrace se musí přihlásit k portálu Azure EA. Po přihlášení se registrace přepne do stavu **Aktivní**.

**Aktivní:** Registrace je přístupná a použitelná. Na portálu Azure EA můžete vytvářet účty a předplatná. Registrace zůstane aktivní až do koncového data smlouvy Enterprise.

**Neurčené období prodloužení:** Tento stav vznikne po dosažení koncového data smlouvy Enterprise. Než registrace EA dosáhne koncového data smlouvy Enterprise, měl by se správce registrace rozhodnout pro jednu z těchto variant:

- Prodloužení registrace přidáním další zálohy na Azure
- Přesun existující registrace do nové registrace
- Migrace do programu Microsoft Online Subscription (MOSP)
- Potvrzení deaktivace všech služeb přidružených k registraci

**Platnost vypršela:** Když registrace EA dosáhne koncového data smlouvy Enterprise, její platnost vyprší. Zákazník EA je výslovně vyřazen z období prodloužení a všechny jeho služby se deaktivují.

Od 1. srpna 2019 nepřijímáme žádné další žádosti o výslovné vyřazení u komerčních zákazníků Azure. Místo toho budou všechny registrace přecházet do neurčeného období prodloužení. Pokud chcete služby Azure přestat používat, uzavřete své předplatné na webu [Azure Portal](https://portal.azure.com). Nebo může žádost o ukončení podat váš partner. U zákazníků s typy smluv pro státní instituce se nic nemění.

**Přesunuto:** Stav Přesunuto se použije u registrací, jejichž přidružené účty a služby byly přesunuty do nové registrace. Registrace se nepřesouvají automaticky, pokud se při prodloužení vygeneruje nové číslo registrace. Aby mohl proběhnout automatický přenos, je třeba do žádosti zákazníka o prodloužení zahrnout předchozí číslo registrace.

## <a name="partner-markup"></a>Marže u partnera

Díky cenovým maržím u partnerů na portálu Azure EA je možné lépe vykazovat náklady pro zákazníky. Portál Azure EA uvádí využití a ceny nakonfigurované partnery pro jejich zákazníky.

Partneři zajišťující správu si u nepřímých smluv Enterprise navyšují cenu o určitou procentuální přirážku. Procentuální přirážka se uplatní u všech sazeb za služby poskytované Microsoftem na webu Azure EA Portal. Jde například o sazby u měřičů, zálohy na Azure a objednávky. Když partner publikuje přirážku, zákazník uvidí náklady spojené s Azure na webu Azure EA Portal. Například v souhrnu využití, na cenících a na stažených sestavách využití.

Od září 2019 mohou partneři uplatňovat marži kdykoli během období. Nemusí tedy čekat do příštího výročí smlouvy.

Microsoft nezpřístupní a nevyužije poskytnutou přirážku a související ceny k žádnému účelu, pokud ho k tomu distribuční partner explicitně neoprávní.

### <a name="how-the-calculation-works"></a>Jak funguje výpočet

Poskytovatel LSP poskytne jednu procentní hodnotu na webu EA Portal.    Všechny komerční informace na portálu se zvýší o počet procent, který tento poskytovatel LSP uvedl. Příklad:

- Zákazník podepíše smlouvu EA se zálohou na Azure ve výši 100 000 USD.
- Sazba měřiče za službu A je 10 USD za hodinu.
- LSP nastaví na webu EA Portal procentní přirážku ve výši 10 %.
- Příklad uvedený níž ukazuje, jak se budou zákazníkům zobrazovat komerční informace:
    - Peněžní zůstatek: 110 000 USD
    - Sazba měřiče pro službu A: 11 USD za hodinu.
    - Informace o využití/hostingu pro službu A, pokud se využívá 100 hodin: 1 100 USD
    - Peněžní zůstatek, který má zákazník k dispozici po odečtení využití služby A: 108 900 USD

### <a name="when-to-use-a-markup"></a>Kdy používat přirážku

Tuto funkci použijte, pokud nastavujete stejnou procentní přirážku pro VŠECHNY komerční transakce v rámci smlouvy EA. To znamená, pokud přirážku používáte pro informace o zálohách na Azure, sazby měřičů, informace o objednávkách atd.

V následujících případech funkci přirážky nepoužívejte:
- Pro zálohu na Azure a sazby měřičů používáte různé sazby.
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

V zobrazení zákazníka v části _Usage Summary_ (Souhrn využití) zkontrolujte cenu s přirážkou pro období zálohy. V zobrazení partnera bude stále dostupná cena Microsoftu. Tato zobrazení je pro přirážku u partnera možné přepínat s využitím přepínače „lidé“ v pravé horní části.

1. Zkontrolujte ceny v ceníku.
1. Změny je možné provést před publikováním, a to volbou **Edit** (Úpravy) na kartě _View Usage Summary > Customer View_ (Zobrazit souhrn využití > Zobrazení zákazníka). 
   
Pro ceny služeb i zůstatky zálohy se použije stejná procentní přirážka. Pokud máte jiný počet procent pro peněžní zůstatek a sazby měřičů nebo různé počty procent pro různé služby, tuto funkci prosím nepoužívejte.

**Krok 3: Publikování**

Po kontrole a ověření cen klikněte na **Publish** (Publikovat).
  
Ceny s přirážkou budou podnikovým správcům k dispozici hned po výběru této možnosti. Přirážku nejde měnit. Musíte ji deaktivovat a znovu začít krokem 1.

### <a name="which-enrollments-have-a-markup-enabled"></a>U kterých prováděcích smluv je přirážka aktivovaná?

Pokud chcete zkontrolovat, jestli je publikovaná přirážka, v levé navigační nabídce klikněte na **Manage** (Spravovat) a potom klikněte na kartu **Enrollment** (Registrace). Zaškrtněte políčko pro registraci a podívejte se na stav přirážky v části _Enrollment Detail_ (Podrobnosti o registraci). Aktuální stav funkce přirážky pro příslušnou smlouvu EA se zobrazí jako Disabled (Neaktivní), Preview nebo Published (Publikovaná).

### <a name="how-can-the-customer-download-usage-estimates"></a>Jak si může zákazník stáhnout odhady využití?

Jakmile se publikuje přirážka partnera, bude mít nepřímý zákazník přístup k měsíčním souborům .csv se zůstatkem a poplatky a k souborům .csv s podrobnými informacemi o využití. Soubory s podrobnými informacemi o využití budou také zahrnovat sazby za prostředek a rozšířené náklady.

### <a name="how-can-i-as-partner-apply-markup-to-existing-ea-customers-that-was-earlier-with-another-partner"></a>Jak mohu jako partner využít přirážku pro stávající zákazníky EA, kteří dřív byli u jiného partnera?
Partneři mohou funkci přirážky (pro Azure EA) využít po zpracování změny distribučního partnera. Není tedy nutné čekat až na další období.


## <a name="resource-prepayment-and-requesting-quota-increases"></a>Záloha na prostředky a žádost o navýšení kvóty

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

## <a name="resource-prepayment"></a>Záloha na prostředky

Microsoft poskytne služby až do úrovně využití, která je součástí zakoupené měsíční zálohy (záloha na služby). Jakékoliv zvýšení úrovní využívání prostředků služeb (například zvýšení počtu běžících výpočetních instancí nebo zvýšení kapacity používaného úložiště) závisí na jejich dostupnosti.

Žádná výše uvedená kvóta není zálohou na služby. Počet simultánně běžících malých výpočetních instancí (nebo odpovídajícího ekvivalentu), které Microsoft poskytne jako součást zálohy na služby, se určí jako podíl počtu hodin vyhrazených malých výpočetních instancí zakoupených v daném měsíci a počtu hodin nejkratšího měsíce v roce (tj. 672 únorových hodin).

## <a name="requesting-a-quota-increase"></a>Žádost o navýšení kvóty

O navýšení kvóty můžete požádat kdykoli, a to odesláním [online žádosti](https://ms.portal.azure.com/). Pro zpracování žádosti zadejte následující informace:

- Účet Microsoft nebo pracovní nebo školní účet přidružený k vlastníkovi účtu vašeho předplatného. Tato e-mailová adresa se používá pro přihlášení k portálu Microsoft Azure a správě vašich předplatných. Ujistěte se také, že tento účet je přidružený k registraci EA.
- Prostředky a objemy, pro které vyžadujete zvýšení kvóty.
- ID předplatného vývojářského portálu Azure přidruženého k vaší službě.
  - Pokud potřebujete informace o tom, jak zjistit ID předplatného, [kontaktujte prosím podporu](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview).

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
