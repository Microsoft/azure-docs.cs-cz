---
title: Aplikační server Java spustit na virtuálním počítači Azure classic
description: Tento kurz používá prostředky, které jsou vytvořené pomocí modelu nasazení classic a ukazuje, jak vytvořit Windows virtuální počítač a nakonfigurovat jej pro spuštění aplikačního serveru Apache Tomcat.
services: virtual-machines-windows
documentationcenter: java
author: rmcmurray
manager: mbaldwin
tags: azure-service-management
ms.assetid: d627aa09-f7d6-4239-8110-f8fc5111b939
ms.service: virtual-machines-windows
ms.workload: web
ms.tgt_pltfrm: vm-windows
ms.devlang: Java
ms.topic: article
ms.date: 04/11/2018
ms.author: robmcm
ms.openlocfilehash: e13228a707e7dae4a4c2505154d01215c40b4716
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/18/2018
ms.locfileid: "31528024"
---
# <a name="how-to-run-a-java-application-server-on-a-virtual-machine-created-with-the-classic-deployment-model"></a>Jak spouštět javový aplikační server na virtuálním počítači vytvořeném pomocí modelu klasického nasazení
> [!IMPORTANT]
> Azure má dva různé modely nasazení pro vytváření a práci s prostředky: [Resource Manager a klasický](../../../resource-manager-deployment-model.md). Tento článek se zabývá pomocí modelu nasazení Classic. Microsoft doporučuje, aby byl ve většině nových nasazení použit model Resource Manager. Šablony Resource Manageru k nasazení webové aplikace Java 8 a Tomcat, najdete v části [zde](https://azure.microsoft.com/documentation/templates/201-web-app-java-tomcat/).
> [!INCLUDE [virtual-machines-common-classic-createportal](../../../../includes/virtual-machines-classic-portal.md)]

S Azure můžete použít virtuální počítač k poskytování funkcí serveru. Jako příklad lze nakonfigurovat virtuální počítač spuštěný v Azure k hostování aplikačního serveru Java, jako je například Apache Tomcat.

Po dokončení tohoto průvodce, budete mít představu o tom, jak vytvořit virtuální počítač běží na Azure a nakonfigurovat jej pro spuštění aplikační server Java. Bude další a provádět následující úlohy:

* Jak vytvořit virtuální počítač, který má Java Development Kit (JDK) už nainstalovaná.
* Jak vzdáleně Přihlaste se k virtuálnímu počítači.
* Postup instalace aplikačního serveru Java – Apache Tomcat – na virtuálním počítači.
* Postup vytvoření koncového bodu pro virtuální počítač.
* Postup otevření portu v bráně firewall pro aplikační server.

Instalace byla dokončena. výsledkem Tomcat, které jsou spuštěny na virtuálním počítači.

![Virtuální počítač se systémem Apache Tomcat][virtual_machine_tomcat]

[!INCLUDE [create-account-and-vms-note](../../../../includes/create-account-and-vms-note.md)]

## <a name="to-create-a-virtual-machine"></a>Vytvoření virtuálního počítače
1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).  
2. Klikněte na tlačítko **vytvořit prostředek**, klikněte na tlačítko **výpočetní**, pak klikněte na tlačítko **zobrazit všechny** v **vybrané aplikace**.
3. Klikněte na tlačítko **JDK**, klikněte na tlačítko **JDK 8** v **JDK** podokně.  
   Virtuální počítač bitové kopie, které podporují **JDK 6** a **JDK 7** jsou k dispozici, pokud máte starší aplikace, které nejsou připravené ke spuštění v JDK 8.
4. V podokně JDK 8 vyberte **Classic**, pak klikněte na tlačítko **vytvořit**.
5. V **Základy** okno:
   1. Zadejte název pro virtuální počítač.
   2. Zadejte název pro správce v **uživatelské jméno** pole. Mějte na paměti, tento název a přiřazené heslo, který následuje v poli Další. Musíte je při vzdálené přihlášení k virtuálnímu počítači.
   3. Zadejte heslo do **nové heslo** pole a znovu ho zadat **potvrzení hesla** pole. Je toto heslo pro účet správce.
   4. Vyberte odpovídající **předplatné**.
   5. Pro **skupiny prostředků**, klikněte na tlačítko **vytvořit nový** a zadejte název nové skupiny prostředků. Nebo klikněte na tlačítko **použít existující** a vyberte jednu z dostupných skupin prostředků.
   6. Vyberte umístění, kde virtuální počítač je umístěn, jako například **jihu USA**.
6. Klikněte na **Další**.
7. V **velikost bitové kopie virtuálního počítače** vyberte **A1 standardní** nebo jiné příslušné bitové kopie.
8. Klikněte na **Vybrat**.

9. V **nastavení** okně klikněte na tlačítko **OK**. Můžete použít výchozí hodnoty, které poskytuje Azure.  
10. V **Souhrn** okně klikněte na tlačítko **OK**.

## <a name="to-remotely-sign-in-to-your-virtual-machine"></a>Chcete-li vzdáleně Přihlaste se k virtuálnímu počítači
1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
2. Klikněte na tlačítko **virtuálních počítačů (klasické)**. V případě potřeby klikněte na tlačítko **další služby** v levém dolním rohu pod kategorií služby. **Virtuálních počítačů (klasické)** položka je uvedena ve **výpočetní** skupiny.
3. Klikněte na název virtuálního počítače, který chcete použít pro přihlášení k aplikaci.
4. Po spuštění virtuálního počítače, nabídce v horní části podokna umožňuje připojení.
5. Klikněte na **Připojit**.
6. Reagujete na výzvy podle potřeby pro připojení k virtuálnímu počítači. Obvykle můžete uložit nebo otevření souboru .rdp, který obsahuje podrobnosti o připojení. Můžete chtít zkopírujte adresu url: port jako poslední část první řádek souboru RDP a vložte ho v aplikaci vzdálené přihlášení.

## <a name="to-install-a-java-application-server-on-your-virtual-machine"></a>Chcete-li nainstalovat aplikační server Java na virtuálním počítači
Aplikační server Java můžete zkopírovat do virtuálního počítače, nebo můžete nainstalovat aplikační server Java prostřednictvím Instalační program.

Tento kurz používá k instalaci Tomcat jako aplikační server Java.

1. Když jsou přihlášení k virtuálnímu počítači, otevřete relaci prohlížeče na [Apache Tomcat](http://tomcat.apache.org/download-80.cgi).
2. Dvakrát klikněte na odkaz pro **Instalační služby systému Windows 32-bit nebo 64-bit**. Když použijete tento postup, Tomcat nainstaluje jako služby systému Windows.
3. Po zobrazení výzvy vyberte spusťte instalační program.
4. V rámci **Apache Tomcat instalace** průvodce, postupujte podle pokynů k instalaci Tomcat. Pro účely tohoto kurzu je v pořádku přijměte výchozí hodnoty. Když se dostanete **dokončování Průvodce instalací Apache Tomcat** dialogové okno, můžete volitelně zkontrolovat **spustit Apache Tomcat** tak, aby měl Tomcat nyní spustit. Klikněte na tlačítko **Dokončit** k dokončení procesu instalace Tomcat.

## <a name="to-start-tomcat"></a>Chcete-li spustit Tomcat

Otevřete příkazový řádek na virtuálním počítači a spuštění příkazu můžete spustit ručně Tomcat **net&nbsp;spustit&nbsp;Tomcat8**.

Jakmile je spuštěna Tomcat, dostanete Tomcat zadáním adresy URL <http://localhost:8080> v prohlížeči virtuálního počítače.

Tomcat spuštěna z externích počítačů najdete potřebujete vytvořit koncový bod a otevřít port.

## <a name="to-create-an-endpoint-for-your-virtual-machine"></a>Chcete-li vytvořit koncový bod pro virtuální počítač
1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
2. Klikněte na tlačítko **virtuálních počítačů (klasické)**.
3. Klikněte na název virtuálního počítače, který je spuštěn aplikační server Java.
4. Klikněte na **Koncové body**.
5. Klikněte na tlačítko **Add** (Přidat).
6. V **přidání koncového bodu** dialogové okno:
   1. Zadejte název pro koncový bod; například **HttpIn**.
   2. Vyberte **TCP** protokolu.
   3. Zadejte **80** pro veřejný port.
   4. Zadejte **8080** pro privátní port.
   5. Vyberte **zakázané** pro plovoucí IP adresa.
   6. Ponechte seznamu řízení přístupu, jako je.
   7. Klikněte **OK** tlačítko a zavřete dialogové okno a vytvořte koncový bod.

## <a name="to-open-a-port-in-the-firewall-for-your-virtual-machine"></a>Otevření portu v bráně firewall pro virtuální počítač
1. Přihlaste se k virtuálnímu počítači.
2. Klikněte na tlačítko **spuštění Windows**.
3. Klikněte na tlačítko **ovládací panely**.
4. Klikněte na tlačítko **systém a zabezpečení**, klikněte na tlačítko **brány Windows Firewall**a potom klikněte na **Upřesnit nastavení**.
5. Klikněte na tlačítko **příchozí pravidla**a potom klikněte na **nové pravidlo**.  
   ![Nové příchozí pravidlo][NewIBRule]
6. Pro **typ pravidla**, vyberte **Port**a potom klikněte na **Další**.  
   ![Nový port příchozí pravidlo][NewRulePort]
7. Na **protokol a porty** obrazovku, vyberte **TCP**, zadejte **8080** jako **konkrétní místního portu**a pak klikněte na tlačítko  **Další**.  
  ![Nové příchozí pravidlo ][NewRuleProtocol]
8. Na **akce** obrazovku, vyberte **povolit připojení**a potom klikněte na **Další**.
   ![Nová akce příchozí pravidlo][NewRuleAction]
9. Na **profil** obrazovky, ujistěte se, že **domény**, **privátní**, a **veřejné** jsou vybrané a pak klikněte na tlačítko **Další**.
   ![Nový profil příchozí pravidlo][NewRuleProfile]
10. Na **název** obrazovky, zadejte název pravidla, jako například **HttpIn** (název pravidla nemusí odpovídat názvu koncového bodu, ale) a potom klikněte na **Dokončit**.  
    ![Nový název příchozí pravidlo][NewRuleName]

V tomto okamžiku by měla být váš web Tomcat viditelný z externího prohlížeče. V okně adresa v prohlížeči zadejte adresu URL ve formátu **http://*vaše\_DNS\_název*. cloudapp.net**, kde ***vaše\_DNS\_název*** je název DNS, který jste zadali při vytváření virtuálního počítače.

## <a name="application-lifecycle-considerations"></a>Aspekty životního cyklu aplikací
* Můžete vytvořit vlastní webový archiv aplikace (WAR) a přidejte ho do **webapps** složky. Například vytvoření základní dynamického webového projektu Java služby stránky (JSP) a exportovat jako soubor WAR. Zkopírujte WAR Apache Tomcat **webapps** složky na virtuálním počítači, spusťte v prohlížeči.
* Ve výchozím nastavení při instalaci služby Tomcat, jinak je nastavená na spustit ručně. Můžete přepnout ji na automatické spouštění pomocí modulu snap-in služby. Spusťte modul snap-in služby kliknutím **spustit Windows**, **nástroje pro správu**a potom **služby**. Dvakrát klikněte **Apache Tomcat** služby a nastavte **typ spuštění** k **automatické**.

    ![Nastavení služby na automatické spouštění.][service_automatic_startup]

    Výhodou vytvoření Tomcat automatické spuštění je, že je spuštěn při restartování virtuálního počítače (například po instalaci aktualizace softwaru, které vyžadují restartování).

## <a name="next-steps"></a>Další postup
Můžete informace o jiných služeb (například Azure Storage, služby service bus a SQL Database), které chcete zahrnout s vaší aplikací v jazyce Java. Zobrazit informace, které jsou k dispozici na [středisko pro vývojáře Java](https://azure.microsoft.com/develop/java/).

[virtual_machine_tomcat]:media/java-run-tomcat-app-server/WA_VirtualMachineRunningApacheTomcat.png

[service_automatic_startup]:media/java-run-tomcat-app-server/WA_TomcatServiceAutomaticStart.png









[NewIBRule]:media/java-run-tomcat-app-server/NewInboundRule.png
[NewRulePort]:media/java-run-tomcat-app-server/NewRulePort.png
[NewRuleProtocol]:media/java-run-tomcat-app-server/NewRuleProtocol.png
[NewRuleAction]:media/java-run-tomcat-app-server/NewRuleAction.png
[NewRuleName]:media/java-run-tomcat-app-server/NewRuleName.png
[NewRuleProfile]:media/java-run-tomcat-app-server/NewRuleProfile.png


<!-- Deleted from the "To create an ednpoint for your virtual mache" 3/17/2017,
     to use the new portal.
6. In the **Add endpoint** dialog box, ensure **Add standalone endpoint** is selected, and then click **Next**.
7. In the **New endpoint details** dialog box:
-->
