---
title: Instalace aktualizace na zařízení GPU pro Azure Stack Edge pro | Microsoft Docs
description: Popisuje, jak použít aktualizace pomocí Azure Portal a místního webového uživatelského rozhraní pro zařízení s GRAFICKÝm rozhraním pro Azure Stack Edge pro a cluster Kubernetes na zařízení.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 01/19/2021
ms.author: alkohli
ms.openlocfilehash: 7db2d2721359d9796b19b3fd6abe32ff16beaec2
ms.sourcegitcommit: 3c3ec8cd21f2b0671bcd2230fc22e4b4adb11ce7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/25/2021
ms.locfileid: "98761796"
---
# <a name="update-your-azure-stack-edge-pro-gpu"></a>Aktualizace GPU Azure Stack Edge pro 

Tento článek popisuje kroky potřebné k instalaci aktualizace na Azure Stack Edge pro pomocí GPU prostřednictvím místního webového uživatelského rozhraní a přes Azure Portal. Aktualizace softwaru a opravy hotfix můžete použít, chcete-li udržovat zařízení Azure Stack Edge pro a přidružený cluster Kubernetes na zařízení v aktuálním stavu.

Postup popsaný v tomto článku byl proveden s použitím jiné verze softwaru, ale proces zůstane stejný jako aktuální verze softwaru.

> [!IMPORTANT]
> - Aktualizace **2101** je aktuální aktualizací a odpovídá:
>   - Verze softwaru zařízení – **2.2.1473.2521**
>   - Kubernetes Server verze **1.17.3**
>   - Verze IoT Edge: **0.1.0-Beta10**
>    
>    Informace o tom, co je nového v této aktualizaci, najdete v [poznámkách k verzi](azure-stack-edge-gpu-2101-release-notes.md).
> - Aby bylo možné použít aktualizaci 2101, musí vaše zařízení používat 2010.
> - Mějte na paměti, že při instalaci aktualizace nebo opravy hotfix dojde k restartování zařízení. Tato aktualizace obsahuje aktualizace softwaru zařízení a aktualizace Kubernetes. Vzhledem k tom, že Azure Stack Edge pro je zařízení s jedním uzlem, dojde k přerušení všech vstupně-výstupních operací a vaše zařízení bude mít výpadek až 1,5 hodin pro aktualizaci.

Chcete-li nainstalovat aktualizace do zařízení, je třeba nejprve nakonfigurovat umístění serveru aktualizací. Po nakonfigurování serveru aktualizací můžete aktualizace použít prostřednictvím uživatelského rozhraní Azure Portal nebo místního webového uživatelského rozhraní.

Každý z těchto kroků je popsaný v následujících částech.

## <a name="configure-update-server"></a>Konfigurace serveru aktualizací

1. V místním webovém uživatelském rozhraní přejdete na   >  **Server aktualizace** konfigurace.
   
    ![Konfigurace aktualizací 1](./media/azure-stack-edge-gpu-install-update/configure-update-server-1.png)

2. V rozevíracím seznamu **Vyberte typ serveru aktualizací** zvolte možnost z Microsoft Update Server (výchozí) nebo Windows Server Update Services.  
   
    Pokud se aktualizuje z Windows Server Update Services, zadejte identifikátor URI serveru. Server na tomto identifikátoru URI nasadí aktualizace do všech zařízení připojených k tomuto serveru.

    ![Konfigurace aktualizací 2](./media/azure-stack-edge-gpu-install-update/configure-update-server-2.png)
    
    Server WSUS se používá ke správě a distribuci aktualizací prostřednictvím konzoly pro správu. Server WSUS může být také zdrojem aktualizací pro další servery WSUS v organizaci. Serveru WSUS, který slouží jako zdroj aktualizací, se říká nadřazený server. V případě implementace WSUS musí být aspoň jeden server WSUS ve vaší síti schopný se připojit k Microsoft Update získat dostupné informace o aktualizacích. Jako správce se můžete rozhodnout v závislosti na zabezpečení sítě a konfiguraci – kolik dalších serverů WSUS se připojuje přímo k Microsoft Update.
    
    Další informace najdete na webu [Windows Server Update Services (WSUS)](/windows-server/administration/windows-server-update-services/get-started/windows-server-update-services-wsus) .

## <a name="use-the-azure-portal"></a>Použití webu Azure Portal

Doporučujeme nainstalovat aktualizace prostřednictvím Azure Portal. Zařízení automaticky vyhledává aktualizace jednou denně. Až budou aktualizace k dispozici, zobrazí se na portálu oznámení. Pak můžete aktualizace stáhnout a nainstalovat.

> [!NOTE]
> Než budete pokračovat v instalaci aktualizací, ujistěte se, že je zařízení v pořádku a zobrazuje se stav **online** .

1. Až budou aktualizace k dispozici pro vaše zařízení, zobrazí se oznámení. Vyberte oznámení nebo na horním panelu příkazů **aktualizujte zařízení**. To vám umožní použít aktualizace softwaru zařízení.

    ![Verze softwaru po aktualizaci](./media/azure-stack-edge-gpu-install-update/portal-update-1.png)

2. V okně **aktualizace zařízení** ověřte, že jste si přečetli licenční smlouvy spojené s novými funkcemi v poznámkách k verzi.

    Můžete si **Stáhnout a nainstalovat** aktualizace nebo **Stáhnout** aktualizace. Tyto aktualizace pak můžete nainstalovat později.

    ![Verze softwaru po aktualizaci 2](./media/azure-stack-edge-gpu-install-update/portal-update-2-a.png)    

    Pokud chcete stáhnout a nainstalovat aktualizace, ověřte možnost, že se aktualizace po dokončení stahování automaticky nainstalují.

    ![Verze softwaru po aktualizaci 3](./media/azure-stack-edge-gpu-install-update/portal-update-2-b.png)

3. Spustí se stahování aktualizací. Zobrazí se oznámení o tom, že probíhá stahování.

    ![Verze softwaru po aktualizaci Update 4](./media/azure-stack-edge-gpu-install-update/portal-update-3.png)

    V Azure Portal se zobrazí také nápis s oznámením. To indikuje průběh stahování.

    ![Verze softwaru po aktualizaci 5](./media/azure-stack-edge-gpu-install-update/portal-update-4.png)

    Můžete vybrat toto oznámení nebo vybrat **aktualizovat zařízení** a zobrazit podrobný stav aktualizace.

    ![Verze softwaru po aktualizaci 6](./media/azure-stack-edge-gpu-install-update/portal-update-5.png)


4. Po dokončení stahování se banner oznámení aktualizuje, aby označoval dokončení. Pokud jste se rozhodli stáhnout a nainstalovat aktualizace, bude instalace automaticky zahájena.

    ![Verze softwaru po aktualizaci 7](./media/azure-stack-edge-gpu-install-update/portal-update-6.png)

    Pokud se rozhodnete stahovat pouze aktualizace, vyberte oznámení a otevřete okno **aktualizace zařízení** . Vyberte **Nainstalovat**.
  
    ![Verze softwaru po aktualizaci 8](./media/azure-stack-edge-gpu-install-update/portal-update-7.png)

5. Zobrazí se oznámení o tom, že probíhá instalace.

    ![Verze softwaru po aktualizaci 9](./media/azure-stack-edge-gpu-install-update/portal-update-8.png)
 
    Portál také zobrazí informační výstrahu, která indikuje, že instalace probíhá. Zařízení přejde do režimu offline a je v režimu údržby.
   
    ![Verze softwaru po aktualizaci 10](./media/azure-stack-edge-gpu-install-update/portal-update-9.png)

6. Vzhledem k tomu, že se jedná o zařízení s jedním uzlem, po instalaci aktualizací se zařízení restartuje. Kritická výstraha během restartování indikuje, že prezenční signál zařízení je ztracený.

    ![Verze softwaru po aktualizaci 11](./media/azure-stack-edge-gpu-install-update/portal-update-10.png)

    Vyberte výstrahu, aby se zobrazila odpovídající událost zařízení.
    
    ![Verze softwaru po aktualizaci 12](./media/azure-stack-edge-gpu-install-update/portal-update-11.png)


7. Po restartování se zařízení opět vloží do režimu údržby a zobrazí se informativní upozornění, které indikuje, že.

    Pokud na horním panelu příkazů vyberete **aktualizovat zařízení** , můžete se podívat na průběh aktualizací.   

8. Po instalaci aktualizací se stav zařízení aktualizuje na **online** . 

    ![Verze softwaru po aktualizaci 13](./media/azure-stack-edge-gpu-install-update/portal-update-14.png)

    V horním panelu příkazů vyberte **aktualizace zařízení**. Ověřte, že se aktualizace úspěšně nainstalovala a že verze softwaru zařízení bude odpovídat.

    ![Verze softwaru po aktualizaci 14](./media/azure-stack-edge-gpu-install-update/portal-update-15.png)

<!--9. You will again see a notification that updates are available. These are the Kubernetes updates. Select the notification or select **Update device** from the top command bar.

    ![Software version after update 15](./media/azure-stack-edge-gpu-install-update/portal-update-16.png)

10. Download the Kubernetes updates. You can see that the package size is different when compared to the previous update package.

    ![Software version after update 16](./media/azure-stack-edge-gpu-install-update/portal-update-17.png)

    The process of installation is identical to that of device updates. First the updates are downloaded.

    ![Software version after update 17](./media/azure-stack-edge-gpu-install-update/portal-update-18.png)    
    
11. Once the updates are downloaded, you can then install the updates. 

    ![Software version after update 18](./media/azure-stack-edge-gpu-install-update/portal-update-19.png)

    As the updates are installed, the device is put into maintenance mode. The device does not restart for the Kubernetes updates. -->

Po úspěšné instalaci softwaru zařízení a aktualizací Kubernetes se oznámení banneru zmizí. Vaše zařízení teď má nejnovější verzi softwaru a Kubernetes zařízení.


## <a name="use-the-local-web-ui"></a>Použití místního webového uživatelského rozhraní

Existují dva kroky při použití místního webového uživatelského rozhraní:

* Stáhnout aktualizaci nebo opravu hotfix
* Instalace aktualizace nebo opravy hotfix

Každý z těchto kroků je podrobně popsán v následujících částech.


### <a name="download-the-update-or-the-hotfix"></a>Stáhnout aktualizaci nebo opravu hotfix

Chcete-li stáhnout aktualizaci, proveďte následující kroky. Aktualizaci si můžete stáhnout z umístění dodaného společností Microsoft nebo z katalogu Microsoft Update.


Chcete-li stáhnout aktualizaci z katalogu Microsoft Update, proveďte následující kroky.

1. Spusťte prohlížeč a přejděte na adresu [https://catalog.update.microsoft.com](https://catalog.update.microsoft.com) .

    ![Prohledávání katalogu](./media/azure-stack-edge-gpu-install-update/download-update-1.png)

2. Do vyhledávacího pole katalogu Microsoft Update zadejte číslo opravy hotfix nebo podmínek pro aktualizaci, kterou chcete stáhnout, do znalostní báze (KB). Zadejte například **Azure Stack Edge pro** a pak klikněte na **Hledat**.
   
    Seznam aktualizací se zobrazí jako **Azure Stack Edge update 2101**.
   
    <!--![Search catalog 2](./media/azure-stack-edge-gpu-install-update/download-update-2-b.png)-->

4. Vyberte **Stáhnout**. Existují dva soubory ke stažení s příponami *SoftwareUpdatePackage.exe* a *Kubernetes_Package.exe* , které odpovídají aktualizacím softwaru zařízení a Kubernetes aktualizací. Stáhněte soubory do složky v místním systému. Můžete také zkopírovat složku do síťové sdílené složky, která je dosažitelná ze zařízení.

### <a name="install-the-update-or-the-hotfix"></a>Instalace aktualizace nebo opravy hotfix

Před instalací aktualizace nebo oprav hotfix se ujistěte, že:

 - Máte aktualizaci nebo opravu hotfix staženou místně na svém hostiteli nebo přístupnou přes sdílenou síťovou složku.
 - Stav zařízení je v pořádku, jak je znázorněno na stránce **Přehled** místního webového uživatelského rozhraní.

   ![aktualizace zařízení](./media/azure-stack-edge-gpu-install-update/local-ui-update-1.png)

Dokončení tohoto postupu trvá přibližně 20 minut. Provedením následujících kroků nainstalujete aktualizaci nebo opravu hotfix.

1. V místním webovém uživatelském rozhraní přejdete na **Údržba**  >  **aktualizace softwaru**. Poznamenejte si verzi softwaru, kterou používáte. 
   
   ![aktualizace zařízení 2](./media/azure-stack-edge-gpu-install-update/local-ui-update-2.png)

2. Zadejte cestu k souboru aktualizace. Můžete také přejít k instalačnímu souboru aktualizace, pokud je umístěn ve sdílené síťové složce. Vyberte soubor aktualizace softwaru s příponou *SoftwareUpdatePackage.exe* .

   ![aktualizace zařízení 3](./media/azure-stack-edge-gpu-install-update/local-ui-update-3-a.png)

3. Vyberte **Použít**.

   ![aktualizace zařízení 4](./media/azure-stack-edge-gpu-install-update/local-ui-update-4.png)

4. Po zobrazení výzvy k potvrzení vyberte **Ano** a pokračujte. Když je zařízení s jedním uzlem, po použití aktualizace se zařízení restartuje a dojde k výpadku. 
   
   ![aktualizace zařízení 5](./media/azure-stack-edge-gpu-install-update/local-ui-update-5.png)

5. Spustí se aktualizace. Po úspěšné aktualizaci zařízení se restartuje. Místní uživatelské rozhraní není v tuto dobu k dispozici.
   
6. Po dokončení restartování přejdete na **přihlašovací** stránku. Chcete-li ověřit, zda byl software zařízení aktualizován, v místním webovém uživatelském rozhraní, navštivte web  >  **aktualizace softwaru** údržba. V aktuální verzi by se zobrazila verze zobrazeného softwaru **Azure Stack Edge 2101**. 

   <!--![update device 6](./media/azure-stack-edge-gpu-install-update/local-ui-update-6.png)-->

7. Nyní aktualizujete verzi Kubernetes softwaru. Opakujte výše uvedené kroky. Zadejte cestu k souboru aktualizace Kubernetes s příponou *Kubernetes_Package.exe* .  

   <!--![update device](./media/azure-stack-edge-gpu-install-update/local-ui-update-7.png)-->

8. Vyberte **použít aktualizaci**.

   ![aktualizace zařízení 7](./media/azure-stack-edge-gpu-install-update/local-ui-update-8.png)

9. Po zobrazení výzvy k potvrzení vyberte **Ano** a pokračujte.

10. Po úspěšné instalaci aktualizace Kubernetes se na zobrazený software v **údržbě**  >  **aktualizace softwaru** nemění.


## <a name="next-steps"></a>Další kroky

Přečtěte si další informace o [správě Azure Stack Edge pro](azure-stack-edge-manage-access-power-connectivity-mode.md).