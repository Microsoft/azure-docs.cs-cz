---
title: Pomocí Azure Machine Learning parametry webové služby | Microsoft Docs
description: Jak používat parametry webové služby Azure Machine Learning Pokud chcete změnit chování modelu při přístupu k webové službě.
services: machine-learning
documentationcenter: ''
author: YasinMSFT
ms.author: yahajiza
manager: hjerez
editor: cgronlun
ms.assetid: c49187db-b976-4731-89d6-11a0bf653db1
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/12/2017
ms.openlocfilehash: 3eb6f08d89daec063841eeb5fff8479c9f2f41fe
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2018
---
# <a name="use-azure-machine-learning-web-service-parameters"></a>Použití parametrů webové služby Azure Machine Learning
Webové služby Azure Machine Learning je vytvořen publikování experimentu, který obsahuje moduly, které se dají konfigurovat parametry. V některých případech můžete změnit chování modulu je spuštěn webovou službu. *Parametry webové služby* umožňují tuto úlohu. 

Běžným příkladem je nastavení [importovat Data] [ reader] modulu tak, aby uživatel publikované webové služby můžete zadat jiný zdroj dat, při přístupu k webové službě. Nebo konfigurace [Export dat] [ writer] modulu tak, aby lze zadat jiný cíl. Mezi další příklady patří změna počtu bitů pro [Hashování] [ feature-hashing] modul, nebo počet požadovaných funkcí pro [na základě filtru výběr funkce] [ filter-based-feature-selection] modulu. 

Můžete nastavit parametry webové služby a je přidružit jeden nebo více parametrů modulu v experimentu a můžete určit, jestli jsou požadované nebo volitelné. Uživatel webové služby můžete poté zadejte hodnoty pro tyto parametry při volání webové služby. 

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]

## <a name="how-to-set-and-use-web-service-parameters"></a>Jak nastavit a používat parametry webové služby
Kliknutím na ikonu vedle parametru pro modul a výběrem "Nastavit jako parametr webové služby" definujete parametr webové služby. Tím se vytvoří nový parametr webové služby a připojí k této parametr modulu. Potom při přístupu k webové službě, může uživatel zadat hodnotu pro parametr webové služby a použije se parametr modulu.

Jakmile definujete parametr webové služby, je k dispozici pro všechny ostatní parametry modulu v experimentu. Pokud definujete webové služby parametr spojený s parametrem pro jeden modul, můžete použít tento stejný parametr webové služby pro ostatní moduly, tak dlouho, dokud parametr očekává hodnoty stejného typu. Například pokud parametr webové služby je číselná hodnota, pak lze být použít pouze pro parametry modulu, které očekávají číselná hodnota. Pokud uživatel nastaví hodnotu pro parametr webové služby, se použijí všechny parametry přidružené modulu.

Můžete se rozhodnout, zda chcete zadat výchozí hodnotu pro parametr webové služby. V takovém případě je parametr volitelný pro uživatele webové služby. Pokud nechcete zadat výchozí hodnotu, uživatel je potřeba zadat hodnotu, při přístupu k webové službě.

Dokumentaci rozhraní API pro webovou službu obsahuje informace o uživateli služby webu na tom, jak zadat parametr webové služby prostřednictvím kódu programu při přístupu k webové službě.

> [!NOTE]
> Dokumentaci rozhraní API pro classic webové služby je zajišťováno prostřednictvím **stránku nápovědy rozhraní API** odkaz ve webové službě **řídicí panel** v nástroji Machine Learning Studio. Dokumentaci k rozhraní API pro novou webovou službu je zajišťováno prostřednictvím [webové služby Azure Machine Learning](https://services.azureml.net/Quickstart) portál **spotřebě** a **rozhraní API Swaggeru** stránky pro webovou službu.
> 
> 

## <a name="example"></a>Příklad:
Jako příklad předpokládejme máme experimentu s [Export dat] [ writer] modul, který odesílá informace do Azure blob storage. Budeme definovat parametr webové služby s názvem "Blob cestu", který umožňuje uživateli webové služby změnit cestu k úložišti objektů blob při přístupu k službě.

1. V nástroji Machine Learning Studio, klikněte na tlačítko [Export dat] [ writer] modul a vyberte ji. Jeho vlastnosti jsou uvedené v podokně vlastností napravo od plátna experimentu.
2. Zadejte typ úložiště:
   
   * V části **zadejte cíl dat**, vyberte "Azure Blob Storage".
   * V části **zadejte typ ověřování**, vyberte "Účet".
   * Zadejte informace o účtu pro úložiště objektů blob v Azure. 

3. Klikněte na ikonu napravo **cesta k objektu blob počínaje kontejneru parametr**. Vypadá takto:
   
   ![Ikona webové služby parametr][icon]
   
   Vyberte "Nastavit jako parametr webové služby".
   
   Položka se přidají pod **parametry webové služby** dole v podokně vlastností s názvem "Cesta k objektu blob počínaje kontejneru". Toto je parametr webové služby, který je teď přidružený k tomuto [Export dat] [ writer] parametr modulu.
4. Chcete-li přejmenovat parametr webové služby, klikněte na název, zadejte "Blob cestu" a stiskněte klávesu **Enter** klíč. 
5. Zadejte výchozí hodnotu pro parametr webové služby, klikněte na ikonu vpravo od názvu, vyberte možnost "Zadejte výchozí hodnotu", zadejte hodnotu (například "container1/output1.csv") a stiskněte klávesu **Enter** klíč.
   
   ![Parametr webové služby][parameter]
6. Klikněte na **Run** (Spustit). 
7. Klikněte na tlačítko **nasazení webové služby** a vyberte **nasazení webové služby [Classic]** nebo **nasazení [nové] webové služby** nasadit webovou službu.

> [!NOTE] 
> K nasazení nové webové služby musí mít dostatečná oprávnění v rámci předplatného, do které, můžete nasazení webové služby. Další informace najdete v tématu [spravovat webové služby pomocí portálu webové služby Azure Machine Learning](manage-new-webservice.md). 

Uživatel webovou službu nyní můžete určit na nové umístění pro [Export dat] [ writer] modulu při přístupu k webové službě.

## <a name="more-information"></a>Další informace
Podrobnější příklad najdete v tématu [parametry webové služby](http://blogs.technet.com/b/machinelearning/archive/2014/11/25/azureml-web-service-parameters.aspx) položku [Machine Learning Blog](http://blogs.technet.com/b/machinelearning/archive/2014/11/25/azureml-web-service-parameters.aspx).

Další informace o přístupu k webové službě Machine Learning najdete v tématu [využívání Azure Machine Learning webové služby](consume-web-services.md).

<!-- Images -->
[icon]: ./media/web-service-parameters/icon.png
[parameter]: ./media/web-service-parameters/parameter.png


<!-- Module References -->
[feature-hashing]: https://msdn.microsoft.com/library/azure/c9a82660-2d9c-411d-8122-4d9e0b3ce92a/
[filter-based-feature-selection]: https://msdn.microsoft.com/library/azure/918b356b-045c-412b-aa12-94a1d2dad90f/
[reader]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/
[writer]: https://msdn.microsoft.com/library/azure/7a391181-b6a7-4ad4-b82d-e419c0d6522c/

