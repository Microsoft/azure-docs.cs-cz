---
title: 'ML Studio (Classic): migrace na Azure Machine Learning'
description: Migrujte z studia (Classic) na Azure Machine Learning pro moderní datovou platformu pro datové vědy.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: how-to
author: xiaoharper
ms.author: zhanxia
ms.date: 03/08/2021
ms.openlocfilehash: fda34a7ee06d35846bcec571e904297d0421c38f
ms.sourcegitcommit: 18a91f7fe1432ee09efafd5bd29a181e038cee05
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/16/2021
ms.locfileid: "103564938"
---
# <a name="migrate-to-azure-machine-learning"></a>Migrace do Azure Machine Learningu

V tomto článku se dozvíte, jak migrovat prostředky studia (Classic) na Azure Machine Learning. V tuto chvíli se k migraci prostředků musíte ručně znovu sestavit.

Azure Machine Learning poskytuje moderní datovou platformu pro datové vědy, která kombinuje přístupy bez kódu a prvního kódu. Další informace o rozdílech mezi Studio (Classic) a Azure Machine Learning najdete v části [vyhodnocení Azure Machine Learning](#step-1-assess-azure-machine-learning) .


## <a name="recommended-approach"></a>Doporučený postup

K migraci na Azure Machine Learning doporučujeme následující postup:

> [!div class="checklist"]
> * Krok 1: vyhodnocení Azure Machine Learning
> * Krok 2: vytvoření plánu migrace
> * Krok 3: opětovné sestavení experimentů a webových služeb
> * Krok 4: integrace klientských aplikací
> * Krok 5: vyčištění prostředků studia (Classic)


## <a name="step-1-assess-azure-machine-learning"></a>Krok 1: vyhodnocení Azure Machine Learning
1. Informace o [Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/); jsou to výhody, náklady a architektura.

1. Porovnejte možnosti Azure Machine Learning a studia (Classic).

    >[!NOTE]
    > Funkce **Návrháře** v Azure Machine Learning poskytuje podobné prostředí pro přetahování do studia (Classic). Azure Machine Learning ale také poskytuje jako alternativu robustní [pracovní postupy pro první kód](../concept-model-management-and-deployment.md) . Tato řada migrace se zaměřuje na návrháře, protože je to nejvíce podobné prostředí studia (Classic).

    [!INCLUDE [aml-compare-classic](../../../includes/machine-learning-compare-classic-aml.md)]

3. Ověřte, že jsou v Návrháři Azure Machine Learning podporovány důležité moduly studia (Classic). Další informace naleznete v tabulce aplikace [Studio (Classic) a Návrhář – mapování](#studio-classic-and-designer-module-mapping) níže.

4. [Vytvořte pracovní prostor Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/how-to-manage-workspace?tabs=azure-portal).

## <a name="step-2-create-a-migration-plan"></a>Krok 2: vytvoření plánu migrace

1. Identifikujte **sady dat**, **modely** a **webové služby** Studio (Classic), které chcete migrovat.

1. Určete, jaký dopad bude mít migrace na vaši firmu.

1. Vytvořte plán migrace.

## <a name="step-3-rebuild-experiments-and-web-services"></a>Krok 3: opětovné sestavení experimentů a webových služeb

1. [Migrujte datové sady na Azure Machine Learning](migrate-register-dataset.md).
1. Použijte návrháře k [opakovanému sestavení experimentů](migrate-rebuild-experiment.md).
1. Pomocí návrháře [znovu nasaďte webové služby](migrate-rebuild-web-service.md).

    >[!NOTE]
    > Azure Machine Learning také podporuje pracovní postupy pro [datové sady](../how-to-create-register-datasets.md), [školení](../how-to-set-up-training-targets.md)a [nasazení](../how-to-deploy-and-where.md).

## <a name="step-4-integrate-client-apps"></a>Krok 4: integrace klientských aplikací

1. Upravte klientské aplikace, které vyvolávají webové služby studia (Classic), aby používaly nové [koncové body Azure Machine Learning](migrate-rebuild-integrate-with-client-app.md).

## <a name="step-5-cleanup-studio-classic-assets"></a>Krok 5: prostředky nástroje CleanUp Studio (Classic)

1. [Vyčistěte prostředky Studio (Classic)](export-delete-personal-data-dsr.md) , abyste se vyhnuli dodatečným poplatkům. Prostředky je vhodné uchovávat pro záložní, dokud neověříte Azure Machine Learning úloh.

## <a name="studio-classic-and-designer-module-mapping"></a>Studio (Classic) a modul návrháře – mapování

V následující tabulce najdete informace o tom, které moduly se mají použít při opakovaném sestavování experimentů v nástroji Studio (Classic) v návrháři.


> [!IMPORTANT]
> Návrhář implementuje moduly prostřednictvím open source balíčků Pythonu místo balíčků C#, jako je Studio (Classic). Z důvodu tohoto rozdílu se výstup modulů návrháře může mírně lišit od jejich protějšků v rámci studia (Classic).


|Kategorie|Studio (Classic) – modul|Náhradní modul návrháře|
|--------------|----------------|--------------------------------------|
|Vstup a výstup dat|-Zadat data ručně </br> -Export dat </br> – Import dat </br> – Model trained Load </br> -UNPACK zip – sady|-Zadat data ručně </br> -Export dat </br> – Import dat|
|Převody formátu dat|– Převést na sdílený svazek clusteru </br> – Převést na datovou sadu </br> – Převést na ARFF </br> – Převést na SVMLight </br> – Převést na TSV|– Převést na sdílený svazek clusteru </br> – Převést na datovou sadu|
|Transformace dat – manipulace|-Přidat sloupce</br> -Přidat řádky </br> -Použít transformaci SQL </br> -Čištění chybějících dat </br> -Převést na hodnoty indikátoru </br> -Upravit metadata </br> – Data spojování </br> -Odstranit duplicitní řádky </br> -Výběr sloupců v datové sadě </br> -Vybrat transformaci sloupců </br> - SMOTE </br> -Group – kategorií hodnoty|-Přidat sloupce</br> -Přidat řádky </br> -Použít transformaci SQL </br> -Čištění chybějících dat </br> -Převést na hodnoty indikátoru </br> -Upravit metadata </br> – Data spojování </br> -Odstranit duplicitní řádky </br> -Výběr sloupců v datové sadě </br> -Vybrat transformaci sloupců </br> - SMOTE|
|Transformace dat – škálování a zmenšení |– Hodnoty klipů </br> -Seskupit data do přihrádek </br> -Normalizovat data </br>– Analýza komponent hlavní součásti |– Hodnoty klipů </br> -Seskupit data do přihrádek </br> -Normalizovat data|
|Transformace dat – ukázka a rozdělení|-Partition a Sample </br> – Rozdělení dat|-Partition a Sample </br> – Rozdělení dat|
|Transformace dat – filtr |– Použít filtr </br> – Filtr JEDLého navýšení </br> – Filtr IIR </br> – Filtr mediánu </br> – Klouzavý průměrový filtr </br> – Filtr prahové hodnoty </br> – Uživatelsky definovaný filtr||
|Transformace dat – učení s počty |– Transformace počítání sestavení </br> – Exportovat tabulku Count </br> -Importovaná tabulka počtu </br> – Transformace počtu sloučení</br>  -Změnit parametry tabulky Count||
|Výběr funkcí |– Výběr funkcí založených na filtrech </br> – Fisher lineární analýzy Discriminant  </br> – Permutace – důležitost funkcí |– Výběr funkcí založených na filtrech </br>  – Permutace – důležitost funkcí|
| Model – klasifikace| – Rozhodovací doménová struktura s více třídami </br> – Jungle rozhodnutí pro více tříd  </br> – Mikrotřída logistické regrese  </br>– Neuronové síť s více třídami  </br>-1-versus-all Multiclass </br>-Two-Class Averageed Perceptron </br>-Two-Class počítač bodu Bayes </br>-Two-Class zvýšení rozhodovacího stromu  </br> -Two-Class rozhodovací doménovou strukturu  </br> – Two-Class rozhodnutí Jungle  </br> -Two-Class Locally-Deep SVM </br> -Two-Class logistické regrese  </br> – Two-Class neuronové síť </br> -Two-Class podporují vektorový počítač.  | – Rozhodovací doménová struktura s více třídami </br>  – Rozhodovací strom zvyšování více tříd  </br> – Mikrotřída logistické regrese </br> – Neuronové síť s více třídami </br> -1-versus-all Multiclass  </br> -Two-Class Averageed Perceptron  </br> -Two-Class zvýšení rozhodovacího stromu  </br> -Two-Class rozhodovací doménovou strukturu </br>-Two-Class logistické regrese </br> – Two-Class neuronové síť </br>-Two-Class podporují vektorový počítač.  |
| Model – clustering| -K-znamená clustering| -K-znamená clustering|
| Model – regrese| – Bayesovského rozhodování lineární regrese  </br> – Zesílené regrese rozhodovacího stromu  </br>– Regrese rozhodovací doménové struktury  </br> – Quantile regrese v rychlé struktuře  </br> – Lineární regrese </br> – Neuronové – regrese sítě </br> – Ordinální regrese Poissonova regrese| – Zesílené regrese rozhodovacího stromu  </br>– Regrese rozhodovací doménové struktury  </br> – Quantile regrese v rychlé struktuře </br> – Lineární regrese  </br> – Neuronové – regrese sítě </br> – Poissonova regrese|
| Model – detekce anomálií| -One-Class SVM  </br> -PCA-Based detekci anomálií | -PCA-Based detekci anomálií|
| Machine Learning – vyhodnotit  | – Model křížového ověřování  </br>– Vyhodnotit model  </br>– Vyhodnotit doporučeného | – Model křížového ověřování  </br>– Vyhodnotit model </br> – Vyhodnotit doporučeného|
| Machine Learning – vlak| – Clustering pro čištění  </br> – Model detekce anomálií vlaku </br>-Výukový model clusteringu  </br> – Matchbox doporučujícího vlaku –</br> Trénování modelu  </br>-Tune model – parametry| – Model detekce anomálií vlaku  </br> -Výukový model clusteringu </br> -Výukový model –</br> – PyTorch model výuky  </br>– SVD doporučení pro vlak  </br>-Vlak v širokém a špičkovém doporučení </br>-Tune model – parametry|
| Machine Learning – skóre| – Použít transformaci  </br>– Přiřazení dat do clusterů  </br>-Skore Matchbox Doporučené </br> – Model skóre|– Použít transformaci  </br> – Přiřazení dat do clusterů </br> – Model obrázku skóre  </br> – Model skóre </br>-Skore SVD Doporučené </br> – Hodnocení široké škály a špičkového doporučeného pro hloubku|
| Moduly knihovny OpenCV| – Import imagí </br>– Předem vyškolená klasifikace imagí v kaskádě | |
| Moduly jazyka Pythonu| -Spustit skript jazyka Python| -Spustit skript jazyka Python  </br> -Vytvořit model Pythonu |
| Moduly jazyka R  | -Spustit skript R  </br> – Vytvoření modelu R| -Spustit skript R|
| Statistické funkce | -Použít matematickou operaci </br>– Základní statistiky COMPUTE  </br>– Výpočet lineární korelace  </br>-Evaluate – funkce pravděpodobnosti  </br>-Nahradit diskrétní hodnoty  </br>– Sumarizace dat  </br>-Test hypotézy pomocí t-test| -Použít matematickou operaci  </br>– Sumarizace dat|
| Analýza textu| – Detekovat jazyky  </br>-Extrahovat klíčové fráze z textu  </br>-Extrahovat N-gram funkcí z textu  </br>-Funkce hashing </br>– Dirichletův přidělování latentních  </br>– Rozpoznávání pojmenovaných entit </br>– Text předběžného zpracování  </br>– Skore pro model dostupné verze 7-10  </br>– Skore pro dostupné model verze 8 </br>– Pro model dostupné verze 7-10  </br>– Pro dostupné model verze 8 |– Převést slovo na vektor </br> -Extrahovat N-gram funkcí z textu </br>-Funkce hashing  </br>– Dirichletův přidělování latentních </br>– Text předběžného zpracování  </br>– Skore pro dostupné model </br> – Pro model dostupné pro vlak|
| Časová řada| Detekce anomálií časové řady | |
| Webová služba | -Vstup </br> – Výstup | -Vstup </br>  - Výstup|
| Počítačové zpracování obrazu| | -Použít transformaci obrázku </br> – Převést na adresář obrázků </br> -Init – transformace obrázku </br> -Rozdělit adresář obrázků  </br> – DenseNet image – klasifikace   </br>– ResNet image – klasifikace |

Další informace o tom, jak používat jednotlivé moduly návrháře, najdete v [Referenční příručce k modulům návrháře](../algorithm-module-reference/module-reference.md).

### <a name="what-if-a-designer-module-is-missing"></a>Co když chybí modul návrháře?

Azure Machine Learning Designer obsahuje Nejoblíbenější moduly z studia (Classic). Zahrnuje také nové moduly, které využívají nejnovější techniky strojového učení. 

Pokud je migrace zablokovaná kvůli chybějícím modulům v návrháři, kontaktujte nás [vytvořením lístku podpory](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="example-migration"></a>Příklad migrace

Následující migrace experimentů zvýrazňuje některé rozdíly mezi Studio (Classic) a Azure Machine Learning.

### <a name="datasets"></a>Datové sady

V aplikaci Studio (Classic) byly **datové sady** uloženy v pracovním prostoru a lze je použít pouze v nástroji Studio (Classic).

![automobil – cena – klasická – datová sada](./media/migrate-overview/studio-classic-dataset.png)

V Azure Machine Learning jsou **datové sady** registrovány v pracovním prostoru a lze je použít napříč všemi Azure Machine Learning. Další informace o výhodách Azure Machine Learning datových sad najdete v tématu [zabezpečení přístupu k datům](../concept-data.md#reference-data-in-storage-with-datasets).

![automobil – cena – AML – datová sada](./media/migrate-overview/aml-dataset.png)

### <a name="pipeline"></a>Kanál

**Experimenty** v nástroji Studio (Classic) obsahovaly logiku zpracování pro vaši práci. Vytvořili jste experimenty s přetahováním modulů.


![automobil – cena – klasický – experiment](./media/migrate-overview/studio-classic-experiment.png)

V Azure Machine Learning **kanály** obsahují logiku zpracování pro vaši práci. Kanály můžete vytvářet buď pomocí modulů přetažení, nebo pomocí psaní kódu.

![automobil – cena – AML-Pipeline](./media/migrate-overview/aml-pipeline.png)

### <a name="web-service-endpoint"></a>Koncový bod webové služby

V nástroji Studio (Classic) se **rozhraní API pro žádosti a odpovědi** používalo pro předpověď v reálném čase. **Rozhraní API pro spuštění dávky** bylo použito pro předpovědi nebo přeškolení dávky.

![automobil – cena – klasický – WebService](./media/migrate-overview/studio-classic-web-service.png)

V Azure Machine Learning jsou **koncové body** v reálném čase používány pro předpověď v reálném čase. **Koncové body kanálu** se používají pro dávkovou předpovědi nebo přeškolení.

![automobil – cena – AML – koncový bod](./media/migrate-overview/aml-endpoint.png)


## <a name="next-steps"></a>Další kroky

V tomto článku jste se dozvěděli o požadavcích na vysokou úroveň migrace na Azure Machine Learning. Podrobný postup najdete v dalších článcích v řadě migrace v rámci studia (Classic):

1. **Přehled migrace**
1. [Migrujte datovou sadu](migrate-register-dataset.md).
1. [Znovu sestavte školicí kanál studia (Classic)](migrate-rebuild-experiment.md).
1. [Opětovné sestavení webové služby studia (Classic)](migrate-rebuild-web-service.md).
1. [Integrujte webovou službu Azure Machine Learning s klientskými aplikacemi](migrate-rebuild-integrate-with-client-app.md).
1. [Migrace spouštěného skriptu R](migrate-execute-r-script.md)






