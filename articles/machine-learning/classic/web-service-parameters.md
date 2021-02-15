---
title: 'ML Studio (Classic): parametry webové služby – Azure'
description: Jak používat Azure Machine Learning parametry webové služby pro úpravu chování modelu při použití webové služby.
services: machine-learning
author: likebupt
ms.author: keli19
ms.assetid: c49187db-b976-4731-89d6-11a0bf653db1
ms.service: machine-learning
ms.subservice: studio-classic
ms.workload: data-services
ms.topic: how-to
ms.date: 01/12/2017
ms.openlocfilehash: 49814d01209b58666c011a6bbd7fe6c328d460f8
ms.sourcegitcommit: e972837797dbad9dbaa01df93abd745cb357cde1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/14/2021
ms.locfileid: "100518330"
---
# <a name="use-azure-machine-learning-studio-classic-web-service-parameters"></a>Použít parametry webové služby Azure Machine Learning Studio (Classic)

**platí pro:** ![ Platí pro. ](../../../includes/media/aml-applies-to-skus/yes.png) Machine Learning Studio (Classic) ![ neplatí pro.](../../../includes/media/aml-applies-to-skus/no.png)[ Azure Machine Learning](../overview-what-is-machine-learning-studio.md#ml-studio-classic-vs-azure-machine-learning-studio)  


Webová služba Azure Machine Learning je vytvořena publikováním experimentu obsahujícího moduly s konfigurovatelnými parametry. V některých případech můžete chtít změnit chování modulu, když je webová služba spuštěná. Tento úkol vám umožní použít *parametry webové služby* . 

Běžným příkladem je nastavení modulu [Import dat][reader] tak, aby uživatel publikované webové služby mohl při použití webové služby zadat jiný zdroj dat. Nebo nakonfigurujte modul [exportu dat][writer] tak, aby bylo možné zadat jiný cíl. Mezi další příklady patří změna počtu bitů pro modul [hash funkcí][feature-hashing] nebo počet požadovaných funkcí pro modul [výběru funkcí založených na filtrech][filter-based-feature-selection] . 

Můžete nastavit parametry webové služby a přidružit je k jednomu nebo více parametrům modulu v experimentu a můžete určit, jestli jsou povinné nebo volitelné. Uživatel webové služby pak může zadat hodnoty pro tyto parametry při volání webové služby. 



## <a name="how-to-set-and-use-web-service-parameters"></a>Nastavení a použití parametrů webové služby
Parametr webové služby definujete tak, že kliknete na ikonu vedle parametru pro modul a vyberete nastavit jako parametr webové služby. Tím se vytvoří nový parametr webové služby a připojí se k parametru tohoto modulu. Potom může uživatel při použití webové služby zadat hodnotu pro parametr webové služby a použije se pro parametr modulu.

Po definování parametru webové služby je tento parametr k dispozici pro všechny ostatní parametry modulu v experimentu. Pokud definujete parametr webové služby přidružený k parametru pro jeden modul, můžete použít stejný parametr webové služby pro jakýkoli jiný modul, pokud parametr očekává stejný typ hodnoty. Například pokud je parametr webové služby číselná hodnota, lze ji použít pouze pro parametry modulu, které očekávají číselnou hodnotu. Když uživatel nastaví hodnotu pro parametr webové služby, použije se u všech přidružených parametrů modulu.

Můžete se rozhodnout, jestli chcete zadat výchozí hodnotu pro parametr webové služby. V takovém případě je parametr pro uživatele webové služby volitelný. Pokud nezadáte výchozí hodnotu, musí uživatel při použití webové služby zadat hodnotu.

Dokumentace k rozhraní API webové služby obsahuje informace o tom, jak zadat parametr webové služby programově při přístupu k webové službě.

> [!NOTE]
> Dokumentace rozhraní API pro klasickou webovou službu je k dispozici prostřednictvím odkazu na **stránku s nápovědu k rozhraní API** na **řídicím panelu** webové služby v Machine Learning Studio (Classic). Dokumentace k rozhraní API pro novou webovou službu je k dispozici prostřednictvím portálu [Azure Machine Learning webové služby](https://services.azureml.net/Quickstart) na stránkách **rozhraní API** pro **využívání** a Swagger pro vaši webovou službu.
> 
> 

## <a name="example"></a>Příklad
Předpokládejme například, že máme experiment s modulem [exportu dat][writer] , který odesílá informace do úložiště objektů BLOB v Azure. Definujeme parametr webové služby s názvem "cesta objektu BLOB", který umožňuje uživateli webové služby měnit cestu k úložišti objektů blob, když je služba dostupná.

1. V Machine Learning Studio (Classic) klikněte na modul [exportovat data][writer] a vyberte ho. Jeho vlastnosti jsou zobrazeny v podokně vlastnosti napravo od plátna experimentu.
2. Zadejte typ úložiště:
   
   * V části **Zadejte cíl dat** vyberte Azure Blob Storage.
   * V části zadejte **typ ověřování** vyberte účet.
   * Zadejte informace o účtu pro úložiště objektů BLOB v Azure. 

3. Klikněte na ikonu napravo od **cesty k objektu BLOB počínaje parametrem kontejneru**. Vypadá takto:
   
   ![Ikona parametru webové služby](./media/web-service-parameters/icon.png)
   
   Vyberte možnost nastavit jako parametr webové služby.
   
   Položka se přidá do části **parametry webové služby** v dolní části podokna vlastností s názvem cesta k objektu BLOB začínajícího kontejnerem. Toto je parametr webové služby, který je nyní přidružen k tomuto parametru modulu [Export dat][writer] .
4. Chcete-li přejmenovat parametr webové služby, klikněte na jeho název, zadejte "cestu k objektu BLOB" a stiskněte klávesu **ENTER** . 
5. Chcete-li zadat výchozí hodnotu pro parametr webové služby, klikněte na ikonu napravo od názvu, vyberte možnost zadat výchozí hodnotu, zadejte hodnotu (například "container1/output1.csv") a stiskněte klávesu **ENTER** .
   
   ![Parametr webové služby](./media/web-service-parameters/parameter.png)
6. Klikněte na **Spustit**. 
7. Klikněte na **nasadit webovou službu** a vyberte **nasadit webovou službu [Classic]** nebo **nasazení webové služby [New]** pro nasazení webové služby.

> [!NOTE] 
> Nasazení nové webové služby vyžaduje dostatečná oprávnění v předplatném, na které nasazujete webovou službu. Další informace najdete v tématu [Správa webové služby pomocí portálu Azure Machine Learning Web Services](manage-new-webservice.md). 

Uživatel webové služby teď může při přístupu k webové službě zadat nový cíl pro modul [Export dat][writer] .

## <a name="more-information"></a>Další informace
Podrobnější příklad najdete v tématu o [parametrech webové služby](/archive/blogs/machinelearning/azureml-web-service-parameters) na [blogu Machine Learning](/archive/blogs/machinelearning/azureml-web-service-parameters).

Další informace o přístupu k webové službě Machine Learning najdete v tématu [jak využívat webovou službu Azure Machine Learning](consume-web-services.md).

<!-- Module References -->
[feature-hashing]: /azure/machine-learning/studio-module-reference/feature-hashing
[filter-based-feature-selection]: /previous-versions/azure/dn905854(v=azure.100)
[reader]: /azure/machine-learning/studio-module-reference/import-data
[writer]: /azure/machine-learning/studio-module-reference/export-data