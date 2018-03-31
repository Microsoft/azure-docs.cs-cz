---
title: Správa rolí v cloudových služeb Azure pomocí sady Visual Studio | Microsoft Docs
description: Zjistěte, jak přidávat a odebírat role ve službě Azure cloud services pomocí sady Visual Studio.
services: visual-studio-online
documentationcenter: na
author: ghogen
manager: douge
editor: ''
ms.assetid: 5ec9ae2e-8579-4e5d-999e-8ae05b629bd1
ms.service: multiple
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 03/21/2017
ms.author: ghogen
ms.openlocfilehash: fb49eaa69f2265f6b312ecbdab1435e7c50236b8
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2018
---
# <a name="managing-roles-in-azure-cloud-services-with-visual-studio"></a>Správa rolí v cloudových služeb Azure pomocí sady Visual Studio
Po vytvoření cloudové služby Azure, můžete do ní přidejte nové role nebo z něj odebrat existující role. Můžete také importovat existující projekt a ho převést na roli. Například můžete importovat webové aplikace ASP.NET a označit ji jako webové role.

## <a name="adding-a-role-to-an-azure-cloud-service"></a>Přidání role do cloudové služby Azure
Následující postup vás provede přidáním role web nebo worker k projektu Azure cloud service v sadě Visual Studio.

1. Vytvořit nebo otevřít projekt Azure cloud service v sadě Visual Studio.

1. V **Průzkumníku**, rozbalte uzel projektu

1. Klikněte pravým tlačítkem myši **role** uzel k zobrazení v místní nabídce. V místní nabídce vyberte **přidat**, pak vyberte existující webovou roli nebo role pracovního procesu z aktuální řešení, nebo vytvořte projekt role web nebo worker. Můžete také vybrat příslušný projekt, jako je například projekt webové aplikace ASP.NET a přidružit projekt role.

    ![Možnosti nabídky k přidání role k projektu Azure cloud service](media/vs-azure-tools-cloud-service-project-managing-roles/add-role.png)

## <a name="removing-a-role-from-an-azure-cloud-service"></a>Odebrání role z cloudové služby Azure
Následující kroky vás provedou odebrání role web nebo worker z projektu Azure cloud service v sadě Visual Studio.

1. Vytvořit nebo otevřít projekt Azure cloud service v sadě Visual Studio.

1. V **Průzkumníku**, rozbalte uzel projektu

1. Rozbalte **role** uzlu.

1. Klikněte pravým tlačítkem na uzel, kterou chcete odebrat a v místní nabídce vyberte **odebrat**. 

    ![Možnosti nabídky přidat roli do cloudové služby Azure](media/vs-azure-tools-cloud-service-project-managing-roles/remove-role.png)

## <a name="readding-a-role-to-an-azure-cloud-service-project"></a>Nové přidání role k projektu Azure cloud service
Pokud odeberete roli z projekt cloudové služby, ale později se rozhodnete přidat roli zpět do projektu, se přidají jenom deklarace role a základní atributy, jako například koncových bodů a diagnostické informace. Žádné další prostředky ani odkazy jsou přidány do `ServiceDefinition.csdef` souboru nebo `ServiceConfiguration.cscfg` souboru. Pokud chcete přidat tyto informace, budete muset ručně přidat zpět do těchto souborů.

Například je možné odebrat role webové služby a později se rozhodnete přidat tato role zpátky do vašeho řešení. Pokud to uděláte, dojde k chybě. Aby se tato chyba, je nutné přidat `<LocalResources>` uvedeného v následující soubor XML zpět do prvku `ServiceDefinition.csdef` souboru. Použijte název role webové služby, které jste přidali zpět do projektu jako součást atributu název pro **<LocalStorage>** element. V tomto příkladu je název role webové služby **WCFServiceWebRole1**.

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
