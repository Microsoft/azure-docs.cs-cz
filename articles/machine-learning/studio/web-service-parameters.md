---
title: Parametry webové služby – Azure Machine Learning Studio (klasické) | Dokumenty společnosti Microsoft
description: Jak použít Parametry webové služby Azure Machine Learning k úpravě chování modelu při přístupu k webové službě.
services: machine-learning
author: xiaoharper
ms.custom: seodec18
ms.author: amlstudiodocs
editor: cgronlun
ms.assetid: c49187db-b976-4731-89d6-11a0bf653db1
ms.service: machine-learning
ms.subservice: studio
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/12/2017
ms.openlocfilehash: d6ddd9603f22bd3820d18be020b9c620cf06aa42
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79204405"
---
# <a name="use-azure-machine-learning-studio-classic-web-service-parameters"></a>Použití parametrů webové služby Azure Machine Learning Studio (klasické)

[!INCLUDE [Notebook deprecation notice](../../../includes/aml-studio-notebook-notice.md)]

Webová služba Azure Machine Learning se vytvoří publikováním experimentu, který obsahuje moduly s konfigurovatelnými parametry. V některých případech můžete chtít změnit chování modulu, zatímco je spuštěna webová služba. *Parametry webové služby* umožňují provést tento úkol. 

Běžným příkladem je nastavení modulu [Import dat][reader] tak, aby uživatel publikované webové služby mohl při přístupu k webové službě zadat jiný zdroj dat. Nebo konfigurace modulu [Export dat][writer] tak, aby bylo možné zadat jiný cíl. Některé další příklady zahrnují změnu počtu bitů pro modul [Hashing funkcí][feature-hashing] nebo počet požadovaných funkcí pro modul [výběr funkcí založený na filtru.][filter-based-feature-selection] 

Můžete nastavit parametry webové služby a přidružit je k jednomu nebo více parametrům modulu v experimentu a můžete určit, zda jsou povinné nebo volitelné. Uživatel webové služby pak může poskytnout hodnoty pro tyto parametry při volání webové služby. 



## <a name="how-to-set-and-use-web-service-parameters"></a>Nastavení a použití parametrů webové služby
Parametr webové služby definujete klepnutím na ikonu vedle parametru modulu a výběrem možnosti "Nastavit jako parametr webové služby". Tím se vytvoří nový parametr webové služby a připojí jej k tomuto parametru modulu. Poté, když je přístupk webové službě, uživatel může zadat hodnotu parametru webové služby a je použita pro parametr modulu.

Jakmile definujete parametr webové služby, je k dispozici pro všechny ostatní parametry modulu v experimentu. Pokud definujete parametr webové služby přidružený k parametru pro jeden modul, můžete použít stejný parametr webové služby pro jakýkoli jiný modul, pokud parametr očekává stejný typ hodnoty. Pokud je například parametr webové služby číselnou hodnotou, lze jej použít pouze pro parametry modulu, které očekávají číselnou hodnotu. Když uživatel nastaví hodnotu parametru webové služby, bude použita pro všechny přidružené parametry modulu.

Můžete se rozhodnout, zda chcete zadat výchozí hodnotu parametru webové služby. Pokud tak učiníte, pak parametr je volitelný pro uživatele webové služby. Pokud nezadáte výchozí hodnotu, je uživatel povinen zadat hodnotu při přístupu k webové službě.

Dokumentace k rozhraní API pro webovou službu obsahuje informace pro uživatele webové služby o tom, jak programově určit parametr webové služby při přístupu k webové službě.

> [!NOTE]
> Dokumentace rozhraní API pro klasickou webovou službu je poskytována prostřednictvím odkazu **na stránku nápovědy rozhraní API** ve webové službě **DASHBOARD** v Machine Learning Studiu (klasika). Dokumentace rozhraní API pro novou webovou službu se poskytuje prostřednictvím portálu [Azure Machine Learning Web Services](https://services.azureml.net/Quickstart) na stránkách rozhraní API pro **využití** a **swagger** pro vaši webovou službu.
> 
> 

## <a name="example"></a>Příklad
Předpokládejme například, že máme experiment s modulem [Export dat,][writer] který odesílá informace do úložiště objektů blob Azure. Definujeme parametr webové služby s názvem "Cesta k objektům blob", který umožňuje uživateli webové služby změnit cestu k úložišti objektů blob při přístupu ke službě.

1. V Machine Learning Studio (klasické) klikněte na modul [Exportovat data][writer] a vyberte ho. Jeho vlastnosti jsou zobrazeny v podokně Vlastnosti napravo od plátna experimentu.
2. Zadejte typ úložiště:
   
   * V části **Zadejte cíl dat**vyberte "Azure Blob Storage".
   * V části **Zadejte typ ověřování**vyberte možnost Účet.
   * Zadejte informace o účtu pro úložiště objektů blob Azure. 

3. Klepněte na ikonu vpravo od **objektu blob cesta začínající parametrem kontejneru**. Vypadá to takhle:
   
   ![Ikona Parametr webové služby](./media/web-service-parameters/icon.png)
   
   Vyberte možnost "Nastavit jako parametr webové služby".
   
   Položka je přidána do **položky Parametry webové služby** v dolní části podokna Vlastnosti s názvem Cesta k objektu blob začínající kontejnerem. Toto je parametr webové služby, který je nyní přidružen k tomuto parametru modulu [Export dat.][writer]
4. Chcete-li parametr webové služby přejmenovat, klepněte na název, zadejte "Cesta objektu blob" a stiskněte klávesu **Enter.** 
5. Chcete-li zadat výchozí hodnotu parametru webové služby, klepněte na ikonu vpravo od názvu, vyberte možnost "Poskytnout výchozí hodnotu", zadejte hodnotu (například "container1/output1.csv") a stiskněte klávesu **Enter.**
   
   ![Parametr webové služby](./media/web-service-parameters/parameter.png)
6. Klepněte na tlačítko **Spustit**. 
7. Klikněte na **Nasadit webovou službu** a vyberte **Deploy Web Service [Classic]** nebo **Deploy Web Service [New]** a nasaďte webovou službu.

> [!NOTE] 
> Chcete-li nasadit novou webovou službu, musíte mít dostatečná oprávnění v předplatném, do kterého nasadíte webovou službu. Další informace najdete [v tématu Správa webové služby pomocí portálu Azure Machine Learning Web Services](manage-new-webservice.md). 

Uživatel webové služby nyní může při přístupu k webové službě zadat nový cíl modulu [Export dat.][writer]

## <a name="more-information"></a>Další informace
Podrobnější příklad najdete v položce [Parametry webové služby](https://blogs.technet.com/b/machinelearning/archive/2014/11/25/azureml-web-service-parameters.aspx) v [blogu machine learningu](https://blogs.technet.com/b/machinelearning/archive/2014/11/25/azureml-web-service-parameters.aspx).

Další informace o přístupu k webové službě Machine Learning najdete v [tématu Jak využívat webovou službu Azure Machine Learning](consume-web-services.md).

<!-- Module References -->
[feature-hashing]: https://msdn.microsoft.com/library/azure/c9a82660-2d9c-411d-8122-4d9e0b3ce92a/
[filter-based-feature-selection]: https://msdn.microsoft.com/library/azure/918b356b-045c-412b-aa12-94a1d2dad90f/
[reader]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/
[writer]: https://msdn.microsoft.com/library/azure/7a391181-b6a7-4ad4-b82d-e419c0d6522c/

