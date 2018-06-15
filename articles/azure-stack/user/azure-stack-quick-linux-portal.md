---
title: Azure zásobníku úvodní – vytvoření virtuálního počítače portálu
description: Azure zásobníku rychlý Start – vytvoření virtuálního počítače s Linuxem pomocí portálu
services: azure-stack
cloud: azure-stack
author: brenduns
manager: femila
ms.service: azure-stack
ms.topic: quickstart
ms.date: 04/24/2018
ms.author: brenduns
ms.reviewer: ''
ms.custom: mvc
ms.openlocfilehash: 2ea07f04d4c566c0add39d75cad3d3a4ed81c6c8
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2018
ms.locfileid: "32152216"
---
# <a name="quickstart-create-a-linux-server-virtual-machine-with-the-azure-stack-portal"></a>Rychlý úvod: vytvoření serveru virtuální počítač s Linuxem pomocí portálu Azure zásobníku

*Platí pro: Azure zásobníku integrované systémy a Azure zásobníku Development Kit*

Virtuální počítače s Ubuntu Server 16.04 LTS můžete vytvořit pomocí portálu Azure zásobníku. Postupujte podle kroků v tomto článku vytváření a používání virtuálního počítače. Tento článek také poskytuje postup:

* Připojte k virtuálnímu počítači pomocí vzdáleného klienta.
* Nainstalujte NGINX webového serveru.
* Vyčistěte vašich prostředků.

## <a name="prerequisites"></a>Požadavky

* **Bitovou kopii systému Linux v zásobníku Azure marketplace**

   Zásobník Azure marketplace neobsahuje bitovou kopii systému Linux ve výchozím nastavení. Než vytvoříte virtuální počítač s Linuxem serveru, ujistěte se, že operátor zásobník Azure poskytuje **Ubuntu Server 16.04 LTS** bitové kopie je nutné. Operátor můžete použít postup popsaný v tématu [stáhnout položky marketplace z Azure do Azure zásobníku](../azure-stack-download-azure-marketplace-item.md) článku.

* **Přístup k klientem SSH**

   Pokud používáte sadu Azure zásobníku Development Kit (ASDK), nemusí mít přístup k klientem SSH. Pokud potřebujete klienta, existuje více balíčků, které zahrnují klientem SSH. Například PuTTY obsahuje služby Klient SSH a generátor klíče SSH (puttygen.exe). Další informace o balíčcích, k dispozici, najdete v následujícím článku na Azure: [jak klíče použití SSH se systémem Windows v Azure](https://docs.microsoft.com/azure/virtual-machines/linux/ssh-from-windows#windows-packages-and-ssh-clients).

   Tento rychlý start využívá PuTTY generování klíčů SSH a připojit k virtuálnímu počítači server Linux. Chcete-li stáhnout a nainstalovat PuTTY, přejděte na [ http://www.putty.org/ ](http://www.putty.org).

## <a name="create-an-ssh-key-pair"></a>Vytvoření páru klíčů SSH

Je nutné dvojici klíčů SSH na dokončení všech kroků v tomto článku. Pokud máte existující dvojici klíčů SSH, můžete tento krok přeskočit.

1. Přejděte do složky instalace PuTTY (výchozí umístění je ```C:\Program Files\PuTTY```) a spusťte ```puttygen.exe```.
2. Ujistěte se, v okně generátor klíče PuTTY **typ klíče pro generování** je nastaven na **RSA**a **počet bitů v generovaný klíč** je nastaven na **2048**. Až budete připraveni, klikněte na tlačítko **generování**.

   ![Generátor klíč konfigurace puTTY](media/azure-stack-quick-linux-portal/Putty01.PNG)

3. Chcete-li vygenerovat klíč, umístěte ukazatel myši náhodně v okně generátor klíče PuTTY.
4. Po dokončení generování klíčů, klikněte na tlačítko **uložit veřejný klíč** a pak klikněte na **uložit privátní klíč** uložení klíče do souborů.

   ![PuTTY klíč generátor výsledky](media/azure-stack-quick-linux-portal/Putty02.PNG)

## <a name="sign-in-to-the-azure-stack-portal"></a>Přihlaste se k portálu Azure zásobníku

Přihlaste se k portálu Azure zásobníku. Na adresu na portálu Azure zásobníku závisí, na který produkt Azure zásobníku se připojujete k:

* Pro Azure zásobníku Development Kit (ASDK) přejděte na: https://portal.local.azurestack.external.
* Pro systém Azure zásobníku integrované přejděte na adresu URL, kterou vaše operátor zásobník Azure poskytuje.

## <a name="create-the-virtual-machine"></a>Vytvoření virtuálního počítače

1. Klikněte na tlačítko **vytvořit prostředek** v levém horním rohu na portálu Azure zásobníku.

2. Vyberte **Compute** a potom vyberte **Ubuntu Server 16.04 LTS**.
3. Klikněte na možnost **Vytvořit**.

4. Zadejte informace o virtuálním počítači. Jako **Typ ověřování** vyberte **Veřejný klíč SSH**. Vložte veřejný klíč SSH, který jste uložili a potom klikněte na **OK**.

   >[!NOTE]
 Zajistěte, aby že se klíče odebrat všechny začínat ani končit mezerou prázdné.

   ![Základy panelu – Konfigurace virtuálního počítače](media/azure-stack-quick-linux-portal/linux-01.PNG)

5. Vyberte **D1_V2** pro virtuální počítač.

   ![Velikost panelu – zvolte velikost virtuálního počítače](media/azure-stack-quick-linux-portal/linux-02.PNG)

6. Na **nastavení** , ponechejte výchozí hodnoty a klikněte na tlačítko **OK**.

7. Na **Souhrn** klikněte na tlačítko **OK** ke spuštění nasazení virtuálního počítače.

## <a name="connect-to-the-virtual-machine"></a>Připojení k virtuálnímu počítači

1. Klikněte na tlačítko **Connect** na stránce virtuálního počítače. Zobrazí řetězec připojení SSH, který je třeba se připojit k virtuálnímu počítači.

   ![Připojit virtuální počítač](media/azure-stack-quick-linux-portal/linux-03.PNG)

2. Otevřete PuTTY.
3. Na **konfigurace PuTTY** obrazovky, které budete používat **kategorie** okna posun nahoru nebo dolů. Přejděte dolů k položce **SSH**, rozbalte položku **SSH**a potom klikněte na **Auth**. Klikněte na tlačítko **Procházet** a vyberte soubor privátního klíče, který jste uložili.

   ![Vyberte PuTTY privátní klíč](media/azure-stack-quick-linux-portal/Putty03.PNG)

4. Posuňte se v **kategorie** okna a pak klikněte na tlačítko **relace**.
5. V **název hostitele (nebo IP adresa)** pole, vložte připojovací řetězec, se na portálu Azure zásobníku. V tomto příkladu je řetězec ```asadmin@192.168.102.34```.

   ![Konfigurace puTTY připojovací řetězec](media/azure-stack-quick-linux-portal/Putty04.PNG)

6. Klikněte na tlačítko **otevřete** otevřít relaci pro virtuální počítač.

   ![Linux relace](media/azure-stack-quick-linux-portal/Putty05.PNG)

## <a name="install-the-nginx-web-server"></a>Instalace webového serveru se NGINX

Použijte následující bash příkazy k aktualizaci zdroje balíčků a instalovat nejnovější balíček NGINX na virtuálním počítači.

```bash
#!/bin/bash

# update package source
sudo apt-get -y update

# install NGINX
sudo apt-get -y install nginx
```

Po dokončení instalace NGINX relace SSH zavřete a otevřete stránce Přehled virtuálního počítače na portálu Azure zásobníku.

## <a name="open-port-80-for-web-traffic"></a>Otevření portu 80 pro webový provoz

Skupina zabezpečení sítě (NSG) zabezpečuje příchozí a odchozí provoz. Při vytvoření virtuálního počítače na portálu Azure zásobníku se na port 22 pro SSH připojení vytvoří pravidlo pro příchozí. Protože tento virtuální počítač je hostitelem webového serveru, je potřeba vytvořit umožnit webové přenosy na portu 80 pravidlo NSG.

1. Na virtuálním počítači **přehled** klikněte na název **skupiny prostředků**.
2. Vyberte **skupinu zabezpečení sítě** pro virtuální počítač. NSG můžete identifikovat pomocí sloupce **Typ**.
3. V levé nabídce v části **nastavení**, klikněte na tlačítko **příchozí pravidla zabezpečení**.
4. Klikněte na tlačítko **Add** (Přidat).
5. Do pole **Název** zadejte **http**. Zkontrolujte, že **Rozsah portů** je nastavený na 80 a **Akce** je nastavená na **Povolit**.
6. Klikněte na tlačítko **OK**.

## <a name="view-the-nginx-welcome-page"></a>Zobrazení úvodní stránky serveru NGINX

NGINX nainstalován, a port 80 otevřete na virtuálním počítači můžete přístup k webovému serveru, pomocí veřejnou IP adresu virtuálního počítače. (Veřejná IP adresa se zobrazí na stránce Přehled virtuálního počítače.)

Otevřete webový prohlížeč a přejděte do ```http://<public IP address>```.

![Úvodní stránka NGINX webového serveru](media/azure-stack-quick-linux-portal/linux-04.PNG)

## <a name="clean-up-resources"></a>Vyčištění prostředků

Vyčistěte prostředky, které už nepotřebujete. Pokud chcete odstranit virtuální počítač a jeho prostředky, vyberte skupinu prostředků na stránce virtuálního počítače a pak klikněte na tlačítko **odstranit**.

## <a name="next-steps"></a>Další postup

V této úvodní jste nasadili základní server virtuální počítač s Linuxem pomocí webového serveru. Další informace o virtuálních počítačích Azure zásobníku, nadále [důležité informace pro virtuální počítače v Azure zásobníku](azure-stack-vm-considerations.md).
