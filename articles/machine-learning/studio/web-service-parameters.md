---
title: Webová služba parametry - Azure Machine Learning Studio | Dokumentace Microsoftu
description: Jak používat parametry webové služby Azure Machine Learning k úpravě chování modelu při přístupu k webové službě.
services: machine-learning
documentationcenter: ''
author: ericlicoding
ms.custom: seodec18
ms.author: amlstudiodocs
editor: cgronlun
ms.assetid: c49187db-b976-4731-89d6-11a0bf653db1
ms.service: machine-learning
ms.component: studio
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/12/2017
ms.openlocfilehash: b929608d64e747d6e768da4ee6627f9e6b09a83a
ms.sourcegitcommit: 7fd404885ecab8ed0c942d81cb889f69ed69a146
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/12/2018
ms.locfileid: "53270078"
---
# <a name="use-azure-machine-learning-studio-web-service-parameters"></a>Použití parametrů webové služby Azure Machine Learning Studio
Webové služby Azure Machine Learning je vytvořen a publikujte experiment, který obsahuje moduly s konfigurovatelné parametry. V některých případech můžete změnit chování modulu, zatímco je webová služba spuštěna. *Webová služba parametry* umožňují provést tuto úlohu. 

Běžným příkladem je nastavení [Import dat] [ reader] modulu tak, aby uživatel publikované webové služby můžete zadat jiný zdroj dat při přístupu k webové službě. Nebo konfigurace [exportovat Data] [ writer] modulu tak, aby se dá nastavit jiný cíl. Mezi další příklady patří změna počtu bitů pro [Hashování] [ feature-hashing] modulu nebo počet požadovaných funkcí pro [výběr součástí na základě filtru] [ filter-based-feature-selection] modulu. 

Můžete nastavit parametry webové služby a přidružit jeden nebo více parametrů modulu v experimentu a můžete určit, jestli jsou povinné nebo volitelné. Uživatele webové službě jim pak můžou hodnoty těchto parametrů při volání webové služby. 



## <a name="how-to-set-and-use-web-service-parameters"></a>Jak nastavit a použití parametrů webové služby
Kliknutím na ikonu vedle parametru pro modul a vyberte "Nastavit jako parametr webové služby" můžete definovat parametr webové služby. Tím se vytvoří nový parametr webové služby a připojí ji k parametru modulu. Potom při přístupu k webové službě, může uživatel zadat hodnotu pro parametr webové služby a je použít pro parametr modulu.

Jakmile definujete parametr webové služby, je k dispozici pro všechny ostatní parametry modulu v experimentu. Pokud definujete parametr webové služby související s parametrem pro jeden modul, můžete použít tento stejný parametr webové služby pro jakýkoli modul jako parametr očekává, že stejný typ hodnoty. Například pokud parametr webové služby je číselnou hodnotou, pak ho jde použít jenom pro parametry modulu, které očekávají číselnou hodnotu. Pokud uživatel nastaví hodnotu pro parametr webové služby, se použijí všechny parametry přidružené modulu.

Můžete se rozhodnout, zda chcete zadat výchozí hodnotu pro parametr webové služby. Pokud tak učiníte, parametr je volitelný pro uživatele webové službě. Pokud nezadáte výchozí hodnotu, uživatel je potřeba zadat hodnotu při přístupu k webové službě.

Dokumentaci k rozhraní API pro webové služby obsahuje informace pro uživatelské web služby o tom, jak prostřednictvím kódu programu zadejte parametr webové služby při přístupu k webové službě.

> [!NOTE]
> Dokumentaci k rozhraní API pro klasické webové služby je zajišťována **stránku nápovědy API** odkaz ve webové službě **řídicí panel** v nástroji Machine Learning Studio. Dokumentaci k rozhraní API pro nové webové služby je zajišťována [Azure Machine Learning Web Services](https://services.azureml.net/Quickstart) na portálu **využívání** a **rozhraní API Swaggeru** pro vaše webové stránky Služba.
> 
> 

## <a name="example"></a>Příklad:
Jako příklad předpokládejme, že máme experimentu s [exportovat Data] [ writer] modul, který odesílá informace do služby Azure blob storage. Budeme definovat parametr webové služby s názvem "Blob cesty", který umožňuje uživateli webové služby změnit cestu k úložišti objektů blob při přístupu k službě.

1. V nástroji Machine Learning Studio, klikněte na tlačítko [exportovat Data] [ writer] modul se vybere. Její vlastnosti jsou uvedeny v podokně vlastností napravo od plátna experimentu.
2. Zadejte typ úložiště:
   
   * V části **zadejte cíl dat**, vyberte "Azure Blob Storage".
   * V části **zadejte typ ověřování**, vyberte "Účet".
   * Zadejte informace o účtu pro úložiště objektů blob v Azure. 

3. Klikněte na ikonu napravo **cestu k začátku s parametrem kontejneru objektů blob**. Vypadá takto:
   
   ![Ikona webové parametr služby][icon]
   
   Vyberte "Nastavit jako parametr webové služby".
   
   Položka se přidá do části **parametrů webové služby** v dolní části podokna Vlastnosti s názvem "Cesty pro začátek s kontejnerem objektů blob". Toto je parametr webové služby, který je teď přidružený k tomuto [exportovat Data] [ writer] parametr modulu.
4. Přejmenovat parametr webové služby, klikněte na název, zadejte "Cestu k objektu Blob" a stiskněte klávesu **Enter** klíč. 
5. Zadejte výchozí hodnotu pro parametr webové služby, klikněte na ikonu napravo od názvu, vyberte "Zadat výchozí hodnotu", zadejte hodnotu (například "container1/output1.csv") a stiskněte klávesu **Enter** klíč.
   
   ![Parametr webové služby][parameter]
6. Klikněte na **Run** (Spustit). 
7. Klikněte na tlačítko **nasadit webovou službu** a vyberte **nasazení webové služby [Classic]** nebo **nasazení [nové] webová služba** nasadit webovou službu.

> [!NOTE] 
> K nasazení nové webové služby musí mít dostatečná oprávnění v rámci předplatného, ke kterému, můžete nasazení webové služby. Další informace najdete v tématu [Správa webové služby pomocí portálu Azure Machine Learning Web Services](manage-new-webservice.md). 

Uživatelské web service můžete nyní zadat nové místo pro [exportovat Data] [ writer] modulu při přístupu k webové službě.

## <a name="more-information"></a>Další informace
Podrobnější příklad naleznete v tématu [parametrů webové služby](http://blogs.technet.com/b/machinelearning/archive/2014/11/25/azureml-web-service-parameters.aspx) položku [Machine Learning Blog](http://blogs.technet.com/b/machinelearning/archive/2014/11/25/azureml-web-service-parameters.aspx).

Další informace o přístupu k webové službě Machine Learning najdete v tématu [jak využívat Azure Machine Learning webové služby](consume-web-services.md).

<!-- Images -->
[icon]: ./media/web-service-parameters/icon.png
[parameter]: ./media/web-service-parameters/parameter.png


<!-- Module References -->
[feature-hashing]: https://msdn.microsoft.com/library/azure/c9a82660-2d9c-411d-8122-4d9e0b3ce92a/
[filter-based-feature-selection]: https://msdn.microsoft.com/library/azure/918b356b-045c-412b-aa12-94a1d2dad90f/
[reader]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/
[writer]: https://msdn.microsoft.com/library/azure/7a391181-b6a7-4ad4-b82d-e419c0d6522c/

