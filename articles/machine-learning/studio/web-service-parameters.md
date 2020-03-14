---
title: Parametry webové služby – Azure Machine Learning Studio (Classic) | Microsoft Docs
description: Jak používat parametry webové služby Azure Machine Learning k úpravě chování modelu při přístupu k webové službě.
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
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/13/2020
ms.locfileid: "79204405"
---
# <a name="use-azure-machine-learning-studio-classic-web-service-parameters"></a>Použít parametry webové služby Azure Machine Learning Studio (Classic)

[!INCLUDE [Notebook deprecation notice](../../../includes/aml-studio-notebook-notice.md)]

Webové služby Azure Machine Learning je vytvořen a publikujte experiment, který obsahuje moduly s konfigurovatelné parametry. V některých případech můžete změnit chování modulu, zatímco je webová služba spuštěna. Tento úkol vám umožní použít *parametry webové služby* . 

Běžným příkladem je nastavení modulu [Import dat][reader] tak, aby uživatel publikované webové služby mohl při použití webové služby zadat jiný zdroj dat. Nebo nakonfigurujte modul [exportu dat][writer] tak, aby bylo možné zadat jiný cíl. Mezi další příklady patří změna počtu bitů pro modul [hash funkcí][feature-hashing] nebo počet požadovaných funkcí pro modul [výběru funkcí založených na filtrech][filter-based-feature-selection] . 

Můžete nastavit parametry webové služby a přidružit jeden nebo více parametrů modulu v experimentu a můžete určit, jestli jsou povinné nebo volitelné. Uživatele webové službě jim pak můžou hodnoty těchto parametrů při volání webové služby. 



## <a name="how-to-set-and-use-web-service-parameters"></a>Jak nastavit a použití parametrů webové služby
Kliknutím na ikonu vedle parametru pro modul a vyberte "Nastavit jako parametr webové služby" můžete definovat parametr webové služby. Tím se vytvoří nový parametr webové služby a připojí ji k parametru modulu. Potom při přístupu k webové službě, může uživatel zadat hodnotu pro parametr webové služby a je použít pro parametr modulu.

Jakmile definujete parametr webové služby, je k dispozici pro všechny ostatní parametry modulu v experimentu. Pokud definujete parametr webové služby související s parametrem pro jeden modul, můžete použít tento stejný parametr webové služby pro jakýkoli modul jako parametr očekává, že stejný typ hodnoty. Například pokud parametr webové služby je číselnou hodnotou, pak ho jde použít jenom pro parametry modulu, které očekávají číselnou hodnotu. Pokud uživatel nastaví hodnotu pro parametr webové služby, se použijí všechny parametry přidružené modulu.

Můžete se rozhodnout, zda chcete zadat výchozí hodnotu pro parametr webové služby. Pokud tak učiníte, parametr je volitelný pro uživatele webové službě. Pokud nezadáte výchozí hodnotu, uživatel je potřeba zadat hodnotu při přístupu k webové službě.

Dokumentaci k rozhraní API pro webové služby obsahuje informace pro uživatelské web služby o tom, jak prostřednictvím kódu programu zadejte parametr webové služby při přístupu k webové službě.

> [!NOTE]
> Dokumentace rozhraní API pro klasickou webovou službu je k dispozici prostřednictvím odkazu na **stránku s nápovědu k rozhraní API** na **řídicím panelu** webové služby v Machine Learning Studio (Classic). Dokumentace k rozhraní API pro novou webovou službu je k dispozici prostřednictvím portálu [Azure Machine Learning webové služby](https://services.azureml.net/Quickstart) na stránkách **rozhraní API** pro **využívání** a Swagger pro vaši webovou službu.
> 
> 

## <a name="example"></a>Příklad
Předpokládejme například, že máme experiment s modulem [exportu dat][writer] , který odesílá informace do úložiště objektů BLOB v Azure. Budeme definovat parametr webové služby s názvem "Blob cesty", který umožňuje uživateli webové služby změnit cestu k úložišti objektů blob při přístupu k službě.

1. V Machine Learning Studio (Classic) klikněte na modul [exportovat data][writer] a vyberte ho. Její vlastnosti jsou uvedeny v podokně vlastností napravo od plátna experimentu.
2. Zadejte typ úložiště:
   
   * V části **Zadejte cíl dat**vyberte Azure Blob Storage.
   * V části zadejte **typ ověřování**vyberte účet.
   * Zadejte informace o účtu pro úložiště objektů blob v Azure. 

3. Klikněte na ikonu napravo od **cesty k objektu BLOB počínaje parametrem kontejneru**. Vypadá takto:
   
   ![Ikona webové parametr služby](./media/web-service-parameters/icon.png)
   
   Vyberte "Nastavit jako parametr webové služby".
   
   Položka se přidá do části **parametry webové služby** v dolní části podokna vlastností s názvem cesta k objektu BLOB začínajícího kontejnerem. Toto je parametr webové služby, který je nyní přidružen k tomuto parametru modulu [Export dat][writer] .
4. Chcete-li přejmenovat parametr webové služby, klikněte na jeho název, zadejte "cestu k objektu BLOB" a stiskněte klávesu **ENTER** . 
5. Pokud chcete zadat výchozí hodnotu pro parametr webové služby, klikněte na ikonu napravo od názvu, vyberte zadat výchozí hodnotu, zadejte hodnotu (například "container1/output1. csv") a stiskněte klávesu **ENTER** .
   
   ![Parametr webové služby](./media/web-service-parameters/parameter.png)
6. Klikněte na **Run** (Spustit). 
7. Klikněte na **nasadit webovou službu** a vyberte **nasadit webovou službu [Classic]** nebo **nasazení webové služby [New]** pro nasazení webové služby.

> [!NOTE] 
> K nasazení nové webové služby musí mít dostatečná oprávnění v rámci předplatného, ke kterému, můžete nasazení webové služby. Další informace najdete v tématu [Správa webové služby pomocí portálu Azure Machine Learning Web Services](manage-new-webservice.md). 

Uživatel webové služby teď může při přístupu k webové službě zadat nový cíl pro modul [Export dat][writer] .

## <a name="more-information"></a>Další informace
Podrobnější příklad najdete v tématu o [parametrech webové služby](https://blogs.technet.com/b/machinelearning/archive/2014/11/25/azureml-web-service-parameters.aspx) na [blogu Machine Learning](https://blogs.technet.com/b/machinelearning/archive/2014/11/25/azureml-web-service-parameters.aspx).

Další informace o přístupu k webové službě Machine Learning najdete v tématu [jak využívat webovou službu Azure Machine Learning](consume-web-services.md).

<!-- Module References -->
[feature-hashing]: https://msdn.microsoft.com/library/azure/c9a82660-2d9c-411d-8122-4d9e0b3ce92a/
[filter-based-feature-selection]: https://msdn.microsoft.com/library/azure/918b356b-045c-412b-aa12-94a1d2dad90f/
[reader]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/
[writer]: https://msdn.microsoft.com/library/azure/7a391181-b6a7-4ad4-b82d-e419c0d6522c/

