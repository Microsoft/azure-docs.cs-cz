---
Název: Použití portálu titleSuffix webových služeb Azure Machine Learning Studio: Azure Machine Learning Studio Popis: Můžete spravovat novou Machine Learning a klasické webové služby pomocí portálu Microsoft Azure Machine Learning Web Services. Protože klasické webové služby a nové webové služby jsou založeny na různé základní technologie, získáte možnosti správy mírně liší pro každý z nich.
Services: machine learningu ms.service: ms.subservice strojového učení: studio ms.topic: článku

Autor: ericlicoding ms.author: amlstudiodocs ms.custom: předchozí ms.author=yahajiza, předchozí Autor = YasinMSFT ms.date: 02/28/2017
---
# <a name="manage-a-web-service-using-the-azure-machine-learning-studio-web-services-portal"></a>Správa webové služby pomocí portálu webových služeb Azure Machine Learning Studio
Můžete spravovat novou Machine Learning a klasické webové služby pomocí portálu Microsoft Azure Machine Learning Web Services. Protože klasické webové služby a nové webové služby jsou založeny na různé základní technologie, získáte možnosti správy mírně liší pro každý z nich.

Na portálu pro Machine Learning Web Services vám umožňuje:

* Sledujte, jak se používá webová služba.
* Konfigurace popisu a aktualizovat klíče pro web service (pouze nové), aktualizovat vaše úložiště účtu klíče (pouze nové), povolte protokolování a povolit nebo zakázat ukázková data.
* Odstraňte webovou službu.
* Vytvoření, odstranění nebo aktualizaci fakturace schémata (pouze nové).
* Přidání a odstranění koncových bodů (pouze Klasický model)



## <a name="permissions-to-manage-new-resources-manager-based-web-services"></a>Oprávnění ke správě nového správce prostředků na základě webové služby

S novými webovými službami jsou nasazeny jako prostředky Azure. V důsledku toho musí mít správná oprávnění k nasazení a Správa nových webových služeb.  Nasazovat nebo spravovat s novými webovými službami musí mít přiřazenou roli přispěvatelem nebo správcem předplatného, které je nasazena webová služba. Pokud vás pozvat jiného uživatele do pracovního prostoru machine learning, je nutné jim přiřadit roli přispěvatelem nebo správcem předplatného mohli nasadit nebo spravovat webové služby. 

Pokud uživatel nemá správná oprávnění pro přístup k prostředkům na portálu Azure Machine Learning Web Services, zobrazí se chybová zpráva při pokusu o nasazení webové služby:

*Nasazení webové služby se nezdařilo. Tento účet nemá dostatečný přístup k předplatnému Azure, který obsahuje tento pracovní prostor. Pokud chcete nasadit webovou službu do Azure, stejný účet musí být pozvaní do pracovního prostoru a být poskytnut přístup k předplatnému Azure, který obsahuje tento pracovní prostor.*

Další informace o vytváření pracovního prostoru najdete v tématu [vytváření a sdílení pracovního prostoru služby Azure Machine Learning](create-workspace.md).

Další informace o nastavení přístupových oprávnění najdete v tématu [správě přístupu pomocí RBAC a webu Azure portal](../../role-based-access-control/role-assignments-portal.md).


## <a name="manage-new-web-services"></a>Správa nových webových služeb
Správa nových webových služeb:

1. Přihlaste se k [Microsoft Azure Machine Learning Web Services](https://services.azureml.net/quickstart) portálu Microsoft Azure pomocí účtu – použijte účet, který je spojen s předplatným Azure.
2. V nabídce klikněte na tlačítko **webových služeb**.

Zobrazí se seznam nasazených webových služeb pro vaše předplatné. 

Pokud chcete spravovat webovou službu, klikněte na webové služby. Na stránce webové služby můžete:

* Klikněte na webové služby k její správě.
* Klikněte na tlačítko fakturační plán pro webovou službu ji aktualizovat.
* Odstraňte webovou službu.
* Kopírovat webovou službu a nasaďte ji do jiné oblasti.

Po kliknutí na webovou službu, otevře se stránka rychlý start webové služby. Webová stránka rychlého startu služby obsahuje dvě možnosti nabídky, které vám pomohou se správou vaší webové služby:

* **Řídicí panel** – umožňuje uživateli zobrazit využití webové služby.
* **KONFIGUROVAT** – umožňuje přidat popisný text, aktualizujte klíč pro účet úložiště přidružený k webové službě a povolit nebo zakázat ukázková data.

### <a name="monitoring-how-the-web-service-is-being-used"></a>Monitorování, jak se používá webová služba
Klikněte na tlačítko **řídicí panel** kartu.

Na řídicím panelu se zobrazí celkové využití webové služby po určitou dobu. Můžete vybrat období pro zobrazení z období rozevírací nabídky v pravém horním rohu grafu využití. Na řídicím panelu zobrazuje následující informace:

* **Žádosti v čase** otevře graf krok počet požadavků za vybrané časové období. Může pomoct určit, jestli máte špičky využití.
* **Žádosti typu žádost-odpověď** zobrazuje celkový počet volání typu žádost-odpověď, Služba obdržela za vybrané časové období a kolik z nich se nezdařilo.
* **Průměrná doba Compute Request-Response** Průměrná doba potřebná k provedení přijaté žádosti.
* **Dávkovat požadavky** zobrazuje celkový počet dávkových žádostí, které Služba obdržela za vybrané časové období a kolik z nich se nezdařilo.
* **Průměrná latence úlohy** Průměrná doba potřebná k provedení přijaté žádosti.
* **Chyby** zobrazí souhrnný počet chyb, ke kterým došlo u volání webové služby.
* **Náklady na služby** zobrazí poplatky za fakturační plán související se službou.

### <a name="configuring-the-web-service"></a>Konfigurace webové služby
Klikněte na tlačítko **KONFIGUROVAT** nabídky.

Můžete aktualizovat následujícími vlastnostmi:

* **Popis** můžete zadat popis pro webovou službu.
* **Název** můžete zadat název pro webovou službu
* **Klíče** umožňuje otočit primární a sekundární klíče rozhraní API.
* **Klíč účtu úložiště** umožňuje aktualizovat klíč pro účet úložiště přidružený k změny webové služby. 
* **Povolit ukázková data** umožňuje poskytovat ukázková data, která slouží k otestování služby typu žádost-odpověď. Pokud jste vytvořili webovou službu v nástroji Machine Learning Studio, ukázkových dat je převzat z dat vaší používané k natrénování modelu. Pokud jste vytvořili službu prostřednictvím kódu programu, data je převzat z příkladu s daty, které jste zadali jako součást balíčku JSON.

### <a name="managing-billing-plans"></a>Správa fakturační plány
Klikněte na tlačítko **plány** nabídky ze stránky rychlý start webové služby. Můžete také kliknout na plán přidružený k konkrétní webové služby ke správě tohoto plánu.

* **Nové** vám umožní vytvořit nový plán.
* **Instance plánu přidat nebo odebrat** umožňuje "škálování" existující plán navyšovat kapacitu.
* **Upgrade a DownGrade** umožňuje "škálování" existující plán navyšovat kapacitu.
* **Odstranit** umožňuje odstranění plánu.

Klikněte na možnost plánu, chcete-li zobrazit jeho řídicí panel. Řídicí panel poskytuje snímku nebo plán využití za vybrané období. Vyberte časové období pro zobrazení, klikněte na tlačítko **období** rozevírací seznam v pravém horním rohu řídicího panelu. 

Řídicí panel plánu obsahuje následující informace:

* **Popis plánu** zobrazí informace o nákladech a kapacitu přidružené k plánu.
* **Plánování použití** zobrazuje počet transakce a výpočetní hodiny, které se účtují podle plánu.
* **Webové služby** zobrazí počet webových služeb, které používají tento plán.
* **TOP webové služby pomocí volání** zobrazuje prvních čtyř webové služby, které jsou volání, které jsou poplatek účtovat podle plánu.
* **Nejčastějších webových služeb podle výpočetních hodin** zobrazuje prvních čtyř webové služby, které používají výpočetní prostředky, které jsou poplatek účtovat podle plánu.

## <a name="manage-classic-web-services"></a>Spravovat klasické webové služby
> [!NOTE]
> Postupy v této části jsou relevantní pro správu klasických webových služeb prostřednictvím portálu Azure Machine Learning Web Services. Informace o správě klasické webové služby Machine Learning Studio a na webu Azure portal najdete v tématu [Správa pracovního prostoru Azure Machine Learning](manage-workspace.md).
> 
> 

Správa klasické webové služby:

1. Přihlaste se k [Microsoft Azure Machine Learning Web Services](https://services.azureml.net/quickstart) portálu Microsoft Azure pomocí účtu – použijte účet, který je spojen s předplatným Azure.
2. V nabídce klikněte na tlačítko **klasické webové služby**.

Ke správě klasických webových služeb, klikněte na tlačítko **klasické webové služby**. Na stránce klasické webové služby můžete:

* Klikněte na webovou službu, chcete-li zobrazit přidružené koncové body.
* Odstraňte webovou službu.

Při správě klasickou webovou službou, můžete spravovat každý z koncových bodů samostatně. Po kliknutí na webové služby na stránce webové služby, otevře se seznam koncových bodů související se službou. 

Na stránce koncový bod webové služby Classic můžete přidat a odstranění koncových bodů služby. Další informace o přidání koncových bodů najdete v tématu [vytváření koncových bodů](create-endpoint.md).

Klikněte na jeden z koncových bodů a otevřete stránku rychlý start webové služby. Na stránce Rychlý start existují dvě možnosti nabídky, které vám pomohou se správou vaší webové služby:

* **Řídicí panel** – umožňuje uživateli zobrazit využití webové služby.
* **KONFIGUROVAT** – umožňuje přidat popisný text, zapnout a vypnout protokolování chyb, aktualizujte klíč pro účet úložiště přidružený k webové službě a povolit a zakázat ukázková data.

### <a name="monitoring-how-the-web-service-is-being-used"></a>Monitorování, jak se používá webová služba
Klikněte na tlačítko **řídicí panel** kartu.

Na řídicím panelu se zobrazí celkové využití webové služby po určitou dobu. Můžete vybrat období pro zobrazení z období rozevírací nabídky v pravém horním rohu grafu využití. Na řídicím panelu zobrazuje následující informace:

* **Žádosti v čase** otevře graf krok počet požadavků za vybrané časové období. Může pomoct určit, jestli máte špičky využití.
* **Žádosti typu žádost-odpověď** zobrazuje celkový počet volání typu žádost-odpověď, Služba obdržela za vybrané časové období a kolik z nich se nezdařilo.
* **Průměrná doba Compute Request-Response** Průměrná doba potřebná k provedení přijaté žádosti.
* **Dávkovat požadavky** zobrazuje celkový počet dávkových žádostí, které Služba obdržela za vybrané časové období a kolik z nich se nezdařilo.
* **Průměrná latence úlohy** Průměrná doba potřebná k provedení přijaté žádosti.
* **Chyby** zobrazí souhrnný počet chyb, ke kterým došlo u volání webové služby.
* **Náklady na služby** zobrazí poplatky za fakturační plán související se službou.

### <a name="configuring-the-web-service"></a>Konfigurace webové služby
Klikněte na tlačítko **KONFIGUROVAT** nabídky.

Můžete aktualizovat následujícími vlastnostmi:

* **Popis** můžete zadat popis pro webovou službu. Popis je povinné pole.
* **Protokolování** umožňuje povolit nebo zakázat chyb do protokolu na koncovém bodu. Další informace o protokolování naleznete v tématu Povolení [protokolování pro webové služby Machine Learning](web-services-logging.md).
* **Povolit ukázková data** umožňuje poskytovat ukázková data, která slouží k otestování služby typu žádost-odpověď. Pokud jste vytvořili webovou službu v nástroji Machine Learning Studio, ukázkových dat je převzat z dat vaší používané k natrénování modelu. Pokud jste vytvořili službu prostřednictvím kódu programu, data je převzat z příkladu s daty, které jste zadali jako součást balíčku JSON.


