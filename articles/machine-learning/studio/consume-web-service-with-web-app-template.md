---
title: Pomocí šablony webové aplikace využívají webové služby Machine Learning | Microsoft Docs
description: Použití šablony webové aplikace v Azure Marketplace využívat prediktivní webové služby v Azure Machine Learning.
keywords: Webová služba, operationalization, REST API strojového učení
services: machine-learning
documentationcenter: ''
author: YasinMSFT
ms.author: yahajiza
manager: hjerez
editor: cgronlun
ms.assetid: e0d71683-61b9-4675-8df5-09ddc2f0d92d
ms.service: machine-learning
ms.component: studio
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/20/2017
ms.openlocfilehash: 03729a5b94b355869367e7f356e299f9afe38f75
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/07/2018
ms.locfileid: "34834994"
---
# <a name="consume-an-azure-machine-learning-web-service-by-using-a-web-app-template"></a>Pomocí šablony webové aplikace využívají webové služby Azure Machine Learning

Můžete vyvinout model prediktivní a nasaďte ho jako Azure webové služby pomocí:
- Azure Machine Learning Studio.
- Nástroje, například R nebo Python. 

Potom můžete přístup k operationalized modelu pomocí rozhraní REST API.

Existuje několik způsobů, jak používat rozhraní REST API a přístup k webové službě. Můžete například napsat aplikaci v jazyce C#, R nebo Python pomocí ukázkový kód pro vás vygeneroval při nasazení webové služby. (Ukázkový kód je k dispozici v [webové služby Machine Learning portál](https://services.azureml.net/quickstart) nebo v řídicím panelu webové služby v nástroji Machine Learning Studio.) Nebo můžete použít sešit aplikace Excel ukázka vytvořili pro vás ve stejnou dobu.

Ale nejrychlejší a nejsnazší způsob pro přístup k webové služby je prostřednictvím šablony webové aplikace k dispozici v [Azure Marketplace](https://azure.microsoft.com/marketplace/web-applications/all/).

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]

## <a name="azure-machine-learning-web-app-templates"></a>Šablony aplikace webové služby Azure Machine Learning
Webové aplikace šablony dostupné v Azure Marketplace můžete vytvořit vlastní webové aplikace, kterou zná vstupní data webové služby a očekávané výsledky. Jediné, co musíte udělat je poskytnout přístup k webové aplikaci pro webové služby a data a šablona nemá rest.

Dvě šablony jsou k dispozici:

* [Šablony aplikace webové služby Azure ML požadavků a odpovědí](https://azure.microsoft.com/marketplace/partners/microsoft/azuremlaspnettemplateforrrs/)
* [Šablony aplikace webové služby provedení dávky Azure ML](https://azure.microsoft.com/marketplace/partners/microsoft/azuremlbeswebapptemplate/)

Každá šablona vytvoří ukázkovou aplikaci ASP.NET pomocí rozhraní API identifikátoru URI a klíč pro webovou službu. Šablony pak nasadí aplikaci jako web do Azure. 

Šablona služby požadavků a odpovědí (záznamy RR) vytvoří webovou aplikaci, která můžete použít k odeslání jednoho řádku dat k webové službě získat jeden výsledek. Spuštění služby Batch (BES) šablona vytvoří webovou aplikaci, která můžete použít k odeslání mnoho řádků dat. Chcete-li získat více výsledků.

Žádné kódování je potřeba použít tyto šablony. Stačí zadat klíč rozhraní API a identifikátor URI a šablona vytvoří aplikaci za vás.

Získat klíč rozhraní API a identifikátor URI požadavku je pro webové služby:

1. V [webové služby portálu](https://services.azureml.net/quickstart), vyberte **webové služby** v horní části. Nebo classic webové služby, vyberte **Classic webové služby**.
2. Vyberte webové služby, která chcete získat přístup.
3. Classic webové služby vyberte koncového bodu, který chcete získat přístup.
4. Vyberte **spotřebě** v horní části.
5. Zkopírujte primární nebo sekundární klíč a uložte ho.
6. Pokud vytváříte šablonu RRS, zkopírujte **požadavků a odpovědí** URI a uložte ho. Pokud vytváříte šablonu BES, zkopírujte **dávkových žádostí** URI a uložte ho.


## <a name="how-to-use-the-request-response-service-template"></a>Jak používat šablony služby požadavků a odpovědí
Použijte následující postup použijte šablony webové aplikace RRS, jak je znázorněno v následujícím diagramu.

![Postup použití RRS webové šablony][image1]


<!--    ![API Key][image3] -->

<!-- This value will look like this:
   
        https://ussouthcentral.services.azureml.net/workspaces/<workspace-id>/services/<service-id>/execute?api-version=2.0&details=true
   
    ![Request URI][image4] -->

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
2. Vyberte **nový**, vyhledejte a vyberte **webové aplikace Azure ML požadavků a odpovědí Service**a potom vyberte **vytvořit**. 
3. V **vytvořit** podokně:
   
   * Zadejte jedinečný název webové aplikace. Adresa URL webové aplikace bude tento název, za nímž následuje **. azurewebsites.net**. Příkladem je **http://carprediction.azurewebsites.net**.
   * Vyberte předplatné Azure a služby, pod kterým běží webové služby.
   * Vyberte **Vytvořit**.
     
   ![Vytvoření webové aplikace][image5]

4. Po dokončení nasazení webové aplikace Azure vyberte **URL** v nastavení aplikace webové stránky v Azure nebo ve webovém prohlížeči zadejte adresu URL. Zadejte například **http://carprediction.azurewebsites.net**.
5. Při prvním spuštění webové aplikace, musíte pro **Post URL rozhraní API** a **klíč rozhraní API**. Zadejte hodnoty, které jste předtím uložili (požadavek URI a klíč API, v uvedeném pořadí). Vyberte **odeslání**.
     
   ![Zadejte identifikátor URI a klíč API post][image6]

6. Zobrazí aplikace webové jeho **konfiguraci webové aplikace** stránka s aktuální nastavení webové služby. Sem můžete provést změny nastavení, která používá webové aplikace.
   
   > [!NOTE]
   > Nastavení zde pouze změna pro tuto webovou aplikaci. Výchozí nastavení webové služby neprojeví. Například, pokud změníte text v **popis** zde nemění Popis zobrazený na řídicím panelu webové služby v nástroji Machine Learning Studio.
   > 
   > 
   
    Až budete hotoví, vyberte **uložit změny**a potom vyberte **přejít na domovskou stránku**.

7. Na domovské stránce můžete zadat hodnoty k odeslání do webové služby. Vyberte **odeslání** když jste hotovi, a vrátí výsledek.

Pokud chcete vrátit **konfigurace** stránky, přejděte na **setting.aspx** stránku webové aplikace. Například, přejděte na **http://carprediction.azurewebsites.net/setting.aspx**. Se zobrazí výzva k opakovanému zadání klíč rozhraní API. Je nutné, pro přístup k stránce a aktualizujte nastavení.

Můžete zastavit, restartovat nebo odstranit webovou aplikaci na portálu Azure stejně jako jiná webové aplikace. Tak dlouho, dokud je spuštěná, můžete procházet domácí webovou adresu a zadejte nové hodnoty.

## <a name="how-to-use-the-batch-execution-service-template"></a>Použití šablony spuštění služby Batch
Šablona BES webové aplikace můžete použít stejným způsobem jako šablonu záznamy o prostředku. Rozdílem je, že můžete vytvořenou webovou aplikaci k odeslání více řádků dat a příjem více výsledků.

Vstupní hodnoty pro spuštění webové služby batch mohou pocházet z Azure Storage nebo místního souboru. Výsledky jsou uloženy v kontejneru úložiště Azure. Ano je nutné kontejner úložiště Azure pro ukládání výsledků, které vrací webové aplikace. Také musíte připravit vstupní data.

![Postup použití BES webové šablony][image2]

1. Postupujte stejným způsobem, k vytvoření webové aplikace BES jako záznamy o prostředku šablony. Ale v takovém případě přejděte na [Azure ML dávky spuštění služby webové aplikace šablona](https://azure.microsoft.com/marketplace/partners/microsoft/azuremlbeswebapptemplate/) otevřete šablonu BES v Azure Marketplace. Vyberte **vytvořit webovou aplikaci**.

2. K určení, kde chcete výsledky uložené, zadejte informace o cílovém kontejneru na domovskou stránku webové aplikace. Také určete, kde webové aplikace můžete získat vstupní hodnoty: v případě místního souboru nebo v kontejner úložiště Azure.
   Vyberte **odeslání**.
   
   ![Informace o úložiště][image7]

Webové aplikace se zobrazí stránka s stav úlohy. Po dokončení úlohy získáte umístění výsledky v Azure Blob storage. Máte také možnost stahování výsledky do místního souboru.

## <a name="for-more-information"></a>Další informace
Další informace o:

* Vytvoření experimentu machine learning s Machine Learning Studio najdete v části [vytvoření prvního experimentu v nástroji Azure Machine Learning Studio](create-experiment.md).
* Tom, jak nasadit experimentu machine learning jako webovou službu, najdete v části [nasazení webové služby Azure Machine Learning](publish-a-machine-learning-web-service.md).
* Další způsoby pro přístup k vaší webové služby, najdete v části [využívání webové služby Azure Machine Learning](consume-web-services.md).

[image1]: media/consume-web-service-with-web-app-template/rrs-web-template-flow.png
[image2]: media/consume-web-service-with-web-app-template/bes-web-template-flow.png
[image3]: media/consume-web-service-with-web-app-template/api-key.png
[image4]: media/consume-web-service-with-web-app-template/post-uri.png
[image5]: media/consume-web-service-with-web-app-template/create-web-app.png
[image6]: media/consume-web-service-with-web-app-template/web-service-info.png
[image7]: media/consume-web-service-with-web-app-template/storage.png
