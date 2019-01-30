---
Název: Používání Studio webové služby s využitím titleSuffix šablony webové aplikace: Azure Machine Learning Studio Popis: Použití šablony webové aplikace v Azure Marketplace k využívání prediktivní webové služby ve službě Azure Machine Learning.
Services: machine learningu ms.service: ms.subservice strojového učení: studio ms.topic: článku

Autor: ericlicoding ms.author: amlstudiodocs ms.custom: seodec18 ms.date: 03/20/2017
---
# <a name="consume-an-azure-machine-learning-studio-web-service-by-using-a-web-app-template"></a>Využívání webové služby Azure Machine Learning Studio pomocí šablony webové aplikace

Můžete vyvíjet prediktivní model a nasadit ho jako webová služba Azure s použitím:
- Azure Machine Learning Studio.
- Nástrojů, jako je R nebo Python.

Poté můžete přistupovat zprovozněné modelu s použitím rozhraní REST API.

Existuje mnoho způsobů, jak používat rozhraní REST API a přístup k webové službě. Můžete například napsat aplikaci C#, R nebo Python s pomocí ukázkového kódu, je vygenerován při nasazení webové služby. (Ukázkový kód je k dispozici v [portálu webových služeb Machine Learning](https://services.azureml.net/quickstart) nebo v řídicím panelu webové služby v nástroji Machine Learning Studio.) Nebo můžete použít ukázkový Excelový sešit vytvořená ve stejnou dobu.

Nejrychlejší a nejjednodušší způsob pro přístup k webové službě je k dispozici v šablonami webových aplikací, ale [Azure Marketplace](https://azure.microsoft.com/marketplace/web-applications/all/).



## <a name="azure-machine-learning-web-app-templates"></a>Šablony aplikace webové služby Azure Machine Learning
Webové aplikace šablony dostupné na webu Azure Marketplace můžete vytvářet vlastní webové aplikace, který zná vstupních dat webové služby a očekávané výsledky. Všechno, co je třeba provést je poskytnout přístup k webové aplikaci pro webové služby a data a udělá zbytek šablony.

K dispozici jsou dvě šablony:

* [Azure Machine Learning studio šablony typu žádost-odpověď služby webové aplikace](https://azure.microsoft.com/marketplace/partners/microsoft/azuremlaspnettemplateforrrs/)
* [Azure Machine Learning studio šablony Batch spuštění služby webové aplikace](https://azure.microsoft.com/marketplace/partners/microsoft/azuremlbeswebapptemplate/)

Každá šablona vytvoří ukázkovou aplikaci ASP.NET pomocí rozhraní API identifikátoru URI a klíče pro webovou službu. Šablonu pak nasadí aplikaci jako web do Azure.

Šablony Service Request-Response (RRS) vytvoří webovou aplikaci, můžete použít k odesílání jednoho řádku dat do webové služby se získá jedna hodnota. Služba Batch Execution (BES) šablony vytvoří webovou aplikaci, která vám umožní odeslat mnoho řádků dat. Chcete-li získat více výsledků.

Tyto šablony používat se vyžaduje bez kódování. Stačí zadat klíč rozhraní API a identifikátor URI a šablona vytvoří aplikaci za vás.

Získání klíče rozhraní API a identifikátor URI žádosti pro webovou službu:

1. V [portálu webových služeb](https://services.azureml.net/quickstart)vyberte **webových služeb** v horní části. Nebo pro klasickou webovou službou, vyberte **klasické webové služby**.
2. Vyberte webové služby, pro které chcete získat přístup.
3. Klasické webové služby vyberte koncový bod, který chcete získat přístup.
4. Vyberte **využívání** v horní části.
5. Primární nebo sekundární klíč si zkopírujte a uložte ho.
6. Pokud vytváříte šablonu RRS, zkopírujte **Request-Response** identifikátor URI a uložte ho. Pokud vytváříte šablonu BES, zkopírujte **dávkových žádostí** identifikátor URI a uložte ho.


## <a name="how-to-use-the-request-response-service-template"></a>Jak použít šablonu služby typu žádost-odpověď
Použijte následující postup použití šablony webové aplikace RRS, jak je znázorněno v následujícím diagramu.

![Postup použití šablony webové RRS][image1]


<!--    ![API Key][image3] -->

<!-- This value will look like this:

        https://ussouthcentral.services.azureml.net/workspaces/<workspace-id>/services/<service-id>/execute?api-version=2.0&details=true

    ![Request URI][image4] -->

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
2. Vyberte **nový**, vyhledejte a vyberte **webová aplikace Azure ML Request Response Service**a pak vyberte **vytvořit**.
3. V **vytvořit** podokna:

   * Zadejte jedinečný název webové aplikace. Adresa URL webové aplikace, bude tento název následovaný **. azurewebsites.net**. Příkladem je **http://carprediction.azurewebsites.net**.
   * Vyberte předplatné Azure a služby, za kterých je spuštěna webová služba.
   * Vyberte **Vytvořit**.

   ![Vytvoření webové aplikace][image5]

4. Jakmile Azure dokončí nasazení webové aplikace, vyberte **URL** na nastavení aplikace webové stránky v Azure nebo ve webovém prohlížeči zadejte adresu URL. Zadejte například **http://carprediction.azurewebsites.net**.
5. Při prvním spuštění webové aplikace, se zobrazí výzva pro **adresy URL rozhraní API příspěvku** a **klíč rozhraní API**. Zadejte hodnoty, které jste předtím uložili (požádat o identifikátor URI a klíč rozhraní API, v uvedeném pořadí). Vyberte **odeslat**.

   ![Zadejte identifikátor URI a klíč rozhraní API po][image6]

6. Webové aplikace zobrazí jeho **konfigurace webové aplikace** stránka s aktuální nastavení webové služby. Tady můžete změníte nastavení, které používá webovou aplikaci.

   > [!NOTE]
   > Změna nastavení tady změní pouze je pro tuto webovou aplikaci. Nezmění výchozí nastavení webové služby. Například, pokud se změní celý text v **popis** tady, nezmění Popis zobrazený na řídicím panelu webové služby v nástroji Machine Learning Studio.
   >
   >

    Jakmile budete hotovi, vyberte **uložit změny**a pak vyberte **přejděte na domovskou stránku**.

7. Na domovské stránce můžete zadat hodnoty odeslat webové službě. Vyberte **odeslat** když to uděláte a vrátí se výsledek.

Pokud chcete vrátit **konfigurace** stránky, přejděte na **setting.aspx** stránka webové aplikace. Například, přejděte na **http://carprediction.azurewebsites.net/setting.aspx**. Budete vyzváni k zadání klíče rozhraní API znovu. Je nutné, aby přístup ke stránce a aktualizujte nastavení.

Zastavení, restartování a odstranění webové aplikace na webu Azure Portal stejně jako jakoukoli jinou webovou aplikaci. Dokud je spuštěná, můžete procházet domácí webovou adresu a zadejte nové hodnoty.

## <a name="how-to-use-the-batch-execution-service-template"></a>Jak používat službu Batch Execution šablony
Šablony BES webové aplikace můžete použít stejným způsobem jako šablona RRS. Rozdíl je, že vám pomůže vytvořené webové aplikace několik řádků dat odesílat a přijímat více výsledků.

Vstupní hodnoty pro spuštění webové služby batch můžou pocházet z Azure Storage nebo do místního souboru. Výsledky jsou uloženy v kontejneru služby Azure storage. Musíte proto kontejner úložiště Azure k ukládání výsledků, které vrací webové aplikace. Také musíte mít vstupní data připravená.

![Postup použití šablony webové BES][image2]

1. Postupujte podle stejného postupu vytvořte BES webové aplikace jako šablony RRS. Ale v takovém případě přejděte na [šablony Batch spuštění služby webové aplikace Azure Machine Learning studio](https://azure.microsoft.com/marketplace/partners/microsoft/azuremlbeswebapptemplate/) otevřete šablonu BES v Tržišti Azure Marketplace. Vyberte **vytvoření webové aplikace**.

2. K určení, kde chcete výsledky uložené, zadejte informace o cílové kontejneru na domovské stránce webové aplikace. Také určit, kde webové aplikace můžete získat vstupních hodnot: v místním souboru nebo v kontejneru služby Azure storage.
   Vyberte **odeslat**.

   ![Informace o úložiště][image7]

Webové aplikace zobrazí stránka s stav úlohy. Když je úloha dokončena, získáte umístění výsledků ve službě Azure Blob storage. Máte také možnost stažení výsledky do místního souboru.

## <a name="for-more-information"></a>Další informace
Další informace:

* Vytvoření experimentu strojového učení pomocí Machine Learning Studio najdete v článku [vytvoření prvního experimentu v Azure Machine Learning Studio](create-experiment.md).
* Nasazení experimentu strojového učení jako webovou službu, najdete v článku [nasazení webové služby Azure Machine Learning](publish-a-machine-learning-web-service.md).
* Další způsoby přístupu k webové službě, naleznete v tématu [jak využívání webové služby Azure Machine Learning](consume-web-services.md).

[image1]: media/consume-web-service-with-web-app-template/rrs-web-template-flow.png
[image2]: media/consume-web-service-with-web-app-template/bes-web-template-flow.png
[image3]: media/consume-web-service-with-web-app-template/api-key.png
[image4]: media/consume-web-service-with-web-app-template/post-uri.png
[image5]: media/consume-web-service-with-web-app-template/create-web-app.png
[image6]: media/consume-web-service-with-web-app-template/web-service-info.png
[image7]: media/consume-web-service-with-web-app-template/storage.png
