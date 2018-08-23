---
title: Správa rolí v cloudových službách Azure pomocí sady Visual Studio | Dokumentace Microsoftu
description: Zjistěte, jak přidávat a odebírat role v cloudových službách Azure pomocí sady Visual Studio.
services: visual-studio-online
author: ghogen
manager: douge
assetId: 5ec9ae2e-8579-4e5d-999e-8ae05b629bd1
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 03/21/2017
ms.author: ghogen
ms.openlocfilehash: 023d70fd1f1ae2f79483f44a84cfedd5d1e39f3f
ms.sourcegitcommit: 30c7f9994cf6fcdfb580616ea8d6d251364c0cd1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/18/2018
ms.locfileid: "42059414"
---
# <a name="managing-roles-in-azure-cloud-services-with-visual-studio"></a>Správa rolí v cloudových službách Azure pomocí sady Visual Studio
Po vytvoření cloudové služby Azure, můžete k němu přidat nové role nebo z něj odebrat existující role. Můžete také naimportovat existující projekt a převeďte jej na roli. Můžete například importovat webové aplikace ASP.NET a určit ji jako webová role.

## <a name="adding-a-role-to-an-azure-cloud-service"></a>Přidáním role cloudové služby Azure
Následující postup vás provede přidáním webové nebo pracovní role do projektu Azure cloud service v sadě Visual Studio.

1. Vytvořte nebo otevřete v sadě Visual Studio projekt cloudové služby Azure.

1. V **Průzkumníka řešení**, rozbalte uzel projektu

1. Klikněte pravým tlačítkem myši **role** uzel k zobrazení v místní nabídce. V místní nabídce vyberte **přidat**, potom vyberte existující webové roli nebo roli pracovního procesu z aktuálního řešení nebo vytvořte projekt webové nebo pracovní role. Můžete také vybrat příslušný projekt, jako je například projektu webové aplikace ASP.NET a přidružte jej k projektu role.

    ![Nabídka možností, jak přidat roli do projektu Azure cloud service](media/vs-azure-tools-cloud-service-project-managing-roles/add-role.png)

## <a name="removing-a-role-from-an-azure-cloud-service"></a>Odebrání role v cloudové službě Azure
Následující kroky vás provedou odebrání webové nebo pracovní role projekt cloudové služby Azure v sadě Visual Studio.

1. Vytvořte nebo otevřete v sadě Visual Studio projekt cloudové služby Azure.

1. V **Průzkumníka řešení**, rozbalte uzel projektu

1. Rozbalte **role** uzlu.

1. Klikněte pravým tlačítkem na uzel, který chcete odebrat a v místní nabídce vyberte **odebrat**. 

    ![Možnosti nabídky přidat roli do cloudové služby Azure](media/vs-azure-tools-cloud-service-project-managing-roles/remove-role.png)

## <a name="readding-a-role-to-an-azure-cloud-service-project"></a>Znovu přidat roli do projektu Azure cloud service
Pokud odeberete roli z projektu cloudové služby, ale později se rozhodnete přidat do projektu role, se přidají pouze deklarace role a základní atributy, jako jsou koncové body a diagnostické informace. Žádné další zdroje informací nebo odkazů jsou přidány do `ServiceDefinition.csdef` souboru nebo `ServiceConfiguration.cscfg` souboru. Pokud chcete přidat tyto informace, budete muset ručně přidat do těchto souborů.

Například je možné odebrat roli webové služby a později se rozhodnete přidat tuto roli zpět do vašeho řešení. Pokud to uděláte, dojde k chybě. K této chybě zabránit, budete muset přidat `<LocalResources>` uvedeného v následující kód XML do prvku `ServiceDefinition.csdef` souboru. Použijte název role webové služby, který jste přidali do projektu jako součást atribut name **<LocalStorage>** elementu. V tomto příkladu je název role webové služby **WCFServiceWebRole1**.

    <WebRole name="WCFServiceWebRole1">
        <Sites>
          <Site name="Web">
            <Bindings>
              <Binding name="Endpoint1" endpointName="Endpoint1" />
            </Bindings>
          </Site>
        </Sites>
        <Endpoints>
          <InputEndpoint name="Endpoint1" protocol="http" port="80" />
        </Endpoints>
        <Imports>
          <Import moduleName="Diagnostics" />
        </Imports>
       <LocalResources>
          <LocalStorage name="WCFServiceWebRole1.svclog" sizeInMB="1000" cleanOnRoleRecycle="false" />
       </LocalResources>
    </WebRole>

## <a name="next-steps"></a>Další postup
- [Konfigurace role pro cloudové služby Azure pomocí sady Visual Studio](vs-azure-tools-configure-roles-for-cloud-service.md)
