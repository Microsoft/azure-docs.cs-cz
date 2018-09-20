---
title: Vytvoření vysoce dostupné databáze MySQL ve službě Azure Stack | Dokumentace Microsoftu
description: Zjistěte, jak vytvořit poskytovatele prostředků MySQL Server hostitelský počítač a vysoce dostupné databáze MySQL pomocí služby Azure Stack.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 09/18/2018
ms.author: jeffgilb
ms.reviewer: quying
ms.openlocfilehash: 17314f921a3d7e8187523b02d3ba47364e7dfb25
ms.sourcegitcommit: ce526d13cd826b6f3e2d80558ea2e289d034d48f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/19/2018
ms.locfileid: "46369044"
---
# <a name="tutorial-create-highly-available-mysql-databases"></a>Kurz: Vytvoření vysoce dostupné databáze MySQL

Jako uživatel tenanta služby Azure Stack můžete nakonfigurovat virtuální počítače serveru pro hostování databází serveru MySQL. Po MySQL je cluster úspěšně vytvořit a spravovat ve službě Azure Stack, uživatelé, kteří se přihlásili k odběru služby MySQL můžete snadno vytvořit vysoce dostupné databáze MySQL.

Tento kurz ukazuje, jak použít k vytvoření položky marketplace služby Azure Stack [MySQL s clusterem replikace](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/bitnami.mysql-cluster). Toto řešení používá k replikaci databáze z hlavního uzlu Konfigurovatelný počet replik několika virtuálních počítačů. Po vytvoření clusteru je potom možné přidat jako Server služby Azure Stack MySQL hostování, a pak uživatelé můžou vytvářet vysoce dostupné databáze MySQL.

> [!IMPORTANT]
> **MySQL s replikací** položky marketplace služby Azure Stack nemusí být k dispozici pro všechna prostředí předplatného cloudu Azure. Ověřte, že položka marketplace není k dispozici ve vašem předplatném, než se pokusíte použijte zbytek tomto tutoral.

Co se naučíte:

> [!div class="checklist"]
> * Vytvořit cluster MySQL serveru z položky marketplace
> * Vytvoření databáze MySQL Azure Stack, který je hostitelem serveru
> * Vytvoření vysoce dostupné databáze MySQL

V tomto kurzu se cluster tři virtuální počítač serveru MySQL se vytvoří a nakonfigurovat pomocí položky marketplace k dispozici služby Azure Stack. 

Před zahájením těchto kroků v tomto kurzu, ujistěte se, že operátor Azure stacku provedl následující položky k dispozici v Tržišti Azure Stack:

> [!IMPORTANT]
> Všechny tyto vyžadovaných pro vytvoření clusteru MySQL.

- [MySQL s replikací](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/bitnami.mysql-cluster). Toto je šablona Bitnami řešení, který se použije pro nasazení clusterů MySQL.
- [Debian 8 "Jessie"](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/credativ.Debian8backports?tab=Overview). Debian 8 "Jessie" s zpětné jádra pro Microsoft Azure poskytnuté credativ. Debian GNU/Linux je jedním z nejoblíbenějších Linuxových distribucí.
- [Vlastní skript pro linux 2.0](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/microsoft.custom-script-linux?tab=Overview). Rozšíření vlastních skriptů je nástroj pro spuštění vašeho virtuálního počítače přizpůsobení úloh příspěvek virtuálního počítače zřizování. Když toto rozšíření se přidá do virtuálního počítače, ho můžete stáhnout skripty z úložiště Azure a spustit na virtuálním počítači. Vlastní rozšíření skriptů úlohy je také možné automatizovat pomocí rutin prostředí Azure PowerShell a rozhraní příkazového řádku Azure víc platforem (xPlat CLI).
- Přístup k virtuálním počítačům pro Linuxové rozšíření 1.4.7. Rozšíření přístupu virtuálních počítačů umožňuje resetovat heslo, klíč SSH nebo konfigurace SSH, takže jste znovu získali přístup k vašemu virtuálnímu počítači. Můžete také přidat nového uživatele pomocí hesla nebo klíče SSH nebo odstranění uživatele pomocí tohoto rozšíření. Toto rozšíření cílí na virtuální počítače s Linuxem.

  > [!TIP]
  > Nebudete moci zobrazit Debian 8 "Jessie" vlastních skriptů pro linux nebo přístup k virtuálním počítačům pro Linux rozšíření marketplace položky při vytváření virtuálního počítače na portálu user portal. Obraťte se na Azure Stack operátor zajistit, že tyto položky byly staženy z Azure před zahájením postupu v tomto kurzu.

Další informace o přidávání položek do Tržiště Azure Stack, najdete v článku [přehled Azure Stack Marketplace](.\.\azure-stack-marketplace.md).

Kromě toho budete potřebovat klienta SSH jako [PuTTY](https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html) k přihlášení do virtuálních počítačů s Linuxem po nasazení.

## <a name="create-a-mysql-server-cluster"></a>Vytvořit cluster MySQL Server 
Použijte postup v této části MySQL serveru k nasazení clusteru s použitím [MySQL s replikací](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/bitnami.mysql-cluster) položky marketplace. Tato šablona nasadí tři instance serveru MySQL nakonfigurované v clusteru s vysokou dostupností MySQL. Ve výchozím nastavení vytvoří následující prostředky:

- Virtuální síť
- Skupina zabezpečení sítě
- Účet úložiště
- Skupinu dostupnosti
- Tři síťová rozhraní (jeden pro každý virtuální počítač výchozí)
- Veřejnou IP adresu (pro primární cluster MySQL virtuálního počítače)
- Tři virtuální počítače s Linuxem k hostování MySQL cluster

> [!NOTE]
> Jako uživatel tenanta s předplatným zajištění schopnosti IaaS (výpočetní, síťové, služeb úložiště) projít tyto kroky na portálu user portal Azure Stack.

1. Přihlaste se k portálu user portal:
    - Nasazení integrovaného systému adresy portálu budou lišit v závislosti na oblasti vašeho řešení a název externí domény. Bude ve formátu https://portal.&lt; *oblast*&gt;.&lt; *Plně kvalifikovaný název domény*&gt;.
    - Pokud používáte Azure Stack Development Kit (ASDK), je uživatelského portálu adresy [ https://portal.local.azurestack.external ](https://portal.local.azurestack.external).

2. Vyberte **\+** **vytvořit prostředek** > **Compute**a potom **MySQL s replikací**.

   ![Nasazení vlastní šablony](media/azure-stack-tutorial-mysqlrp/createcluster1.png)

3. Zadejte informace o základní nasazení na **Základy** stránky. Zkontrolujte výchozí hodnoty a podle potřeby a klikněte na **OK**.<br><br>Minimálně poskytnout následující:
   - Název nasazení (výchozí hodnota je mysql)
   - Kořenové heslo aplikace. Zadejte alfanumerické heslo 12 znaků s **žádné speciální znaky**
   - Název databáze aplikace (výchozí hodnota je bitnami)
   - Počet replik databáze MySQL virtuálních počítačů určených k vytvoření (výchozí hodnota je 2)
   - Vyberte předplatné, které chcete použít
   - Vyberte skupinu prostředků nebo vytvořte novou
   - Vyberte umístění (výchozí hodnota je místní pro ASDK)

   ![Základní informace o nasazení](media/azure-stack-tutorial-mysqlrp/createcluster2.png)

4. Na **konfiguraci prostředí** stránky, zadejte následující informace a klikněte na **OK**: 
   - Heslo nebo SSH veřejný klíč pro ověřování (SSH secure shell). Pokud použijete heslo, musí obsahovat písmena, číslice a **můžete** obsahovat speciální znaky
   - Velikost virtuálního počítače (výchozí hodnota je virtuální počítače Standard D1 v2)
   - Velikost v GB, klikněte na tlačítko datového disku **OK**

   ![Konfigurace prostředí](media/azure-stack-tutorial-mysqlrp/createcluster3.png)

5. Zkontrolujte nasazení **Souhrn**. Volitelně můžete stáhnout přizpůsobenou šablonu a parametry a potom klikněte na **OK**.

   ![Souhrn](media/azure-stack-tutorial-mysqlrp/createcluster4.png)

6. Klikněte na tlačítko **vytvořit** na **koupit** stránku ke spuštění nasazení.

   ![Koupit](media/azure-stack-tutorial-mysqlrp/createcluster4.png)

    > [!NOTE]
    > Nasazení bude trvat přibližně hodinu. Ujistěte se, že nasazení bylo dokončeno a MySQL cluster zcela nakonfigurované než budete pokračovat. 

7. Po všechna nasazení byly úspěšně dokončeny, zkontrolujte položky skupiny prostředků a vyberte **mysqlip** položky veřejné IP adresy. Zaznamenejte veřejné IP adresy a úplný plně kvalifikovaný název domény veřejné IP adresy pro cluster.<br><br>Je potřeba zadat operátor Azure stacku, aby mohli vytvářet na hostitelský server MySQL využívá tento cluster MySQL.


### <a name="create-a-network-security-group-rule"></a>Vytvoření pravidla skupiny zabezpečení sítě
Ve výchozím nastavení bez veřejného přístupu nakonfigurované pro MySQL do virtuálního počítače tenanta. Pro poskytovatele prostředků Azure Stack MySQL připojit a spravovat MySQL cluster je potřeba vytvořit příchozí pravidlo zabezpečení sítě (NSG) skupiny.

1. Na portálu user portal, přejděte do skupiny prostředků vytvořené při nasazování clusteru MySQL a vybrat skupinu zabezpečení sítě (**výchozí podsíť sg**):

   ![open (otevírá)](media/azure-stack-tutorial-mysqlrp/nsg1.png)

2. Vyberte **příchozí pravidla zabezpečení** a potom klikněte na tlačítko **přidat**.<br><br>Zadejte **3306** v **rozsah cílových portů** a volitelně také popis v **název** a **popis** pole. Klikněte na Přidat a zavřete dialogové okno pravidla zabezpečení příchozích dat.

   ![open (otevírá)](media/azure-stack-tutorial-mysqlrp/nsg2.png)

### <a name="configure-external-access-to-the-mysql-cluster"></a>Konfigurace externího přístupu k MySQL cluster
Předtím, než je MySQL cluster se dá přidat jako hostitele služby Azure Stack MySQL serveru, musí být povolená externí přístup.

1. Pomocí klienta SSH, v tomto příkladu [PuTTY](https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html), přihlaste se k primární počítač MySQL na počítači, který může přistupovat k veřejné IP adresy. Primární název virtuálního počítače MySQL většinou končí **0** a má veřejnou IP adresu přiřazenou.<br><br>Použít veřejné IP adresy a protokolu k virtuálnímu počítači k uživatelskému jménu **bitnami** a heslo aplikace, který jste vytvořili dříve bez speciálních znaků.

   ![LinuxLogin](media/azure-stack-tutorial-mysqlrp/bitnami1.png)


2. V okně Klient SSH použijte následující příkaz k zajištění, že služba bitnami je aktivní a v chodu. Po zobrazení výzvy znovu zadali heslo bitnami:

   `sudo service bitnami status`

   ![Zkontrolujte službu](media/azure-stack-tutorial-mysqlrp/bitnami2.png)

3. Vytvoření uživatelského účtu vzdáleného přístupu použije hostující Server MySQL Azure Stack pro připojení k MySQL a poté ukončete klienta SSH.<br><br>Spusťte následující příkazy a přihlaste se jako uživatel root, pomocí hesla kořenového vytvořili dříve, MySQL a vytvoření nového správce uživatele, nahraďte *\<uživatelské jméno\>* a *\<heslo\>* podle potřeby pro vaše prostředí. V tomto příkladu je pojmenována uživatele, který se má vytvořit **sqlsa** a silné heslo se používá:

   ```mysql
   mysql -u root -p
   create user <username>@'%' identified by '<password>';
   grant all privileges on *.* to <username>@'%' with grant option;
   flush privileges;
   ```
   ![Vytvoření uživatele s rolí správce](media/azure-stack-tutorial-mysqlrp/bitnami3.png)


4. Zaznamenejte informace o novém uživateli MySQL.<br><br>Je potřeba zadat uživatelské jméno a heslo, společně s veřejnou IP adresu nebo úplný plně kvalifikovaný název domény veřejné IP adresy pro cluster, operátorovi Azure Stack, aby mohli vytvářet na hostitelský server MySQL pomocí tento cluster MySQL.


## <a name="create-an-azure-stack-mysql-hosting-server"></a>Vytvoření databáze MySQL Azure Stack, který je hostitelem serveru
Po clusteru serveru MySQL byla vytvořena a správně nakonfigurované, musí operátor Azure stacku vytvořit Server služby Azure Stack MySQL hostování zpřístupnit další kapacitu pro uživatele k vytváření databází. 

Zkontrolujte zajištění operátor Azure stacku veřejnou IP adresu nebo úplný plně kvalifikovaný název domény pro veřejnou IP adresu clusteru MySQL primárního virtuálního počítače, která byla zaznamenána dříve při vytvoření skupiny prostředků clusteru MySQL (**mysqlip**). Kromě toho operátor, který se bude znát MySQL Server ověřování pověření, které jste vytvořili pro vzdálený přístup ke clusteru databázi MySQL.

> [!NOTE]
> Tento krok se musí spouštět z portálu pro správu služby Azure Stack nezavřel operátor Azure stacku.

MySQL cluster veřejnou IP adresu a MySQL přihlašovací údaje pro ověřování poskytované uživatel tenanta, operátor Azure stacku umožní nyní [vytvořit nový cluster MySQL pomocí hostování serveru MySQL](.\.\azure-stack-mysql-resource-provider-hosting-servers.md#connect-to-a-mysql-hosting-server). 

Ujistěte se také, že operátor Azure stacku vytvořil, plánů a nabízí uživatelům zpřístupnit vytvoření databáze MySQL. Operátor, který se bude muset přidat **Microsoft.MySqlAdapter** služby k plánu a vytvořit novou kvótu speciálně pro vysoce dostupné databáze. Další informace o vytváření plánů najdete v tématu [plán, nabídky, kvót a předplatného přehled](.\.\azure-stack-plan-offer-quota-overview.md).

> [!TIP]
> **Microsoft.MySqlAdapter** služba nebude k dispozici pro přidání do plánů až [nasazení poskytovatele prostředků MySQL Server](.\.\azure-stack-mysql-resource-provider-deploy.md).



## <a name="create-a-highly-available-mysql-database"></a>Vytvoření vysoce dostupné databáze MySQL
Po MySQL cluster byla vytvořena, konfiguraci a přidat jako Server služby Azure Stack MySQL hostování operátorem Azure Stack, uživatel tenanta s předplatným, včetně schopnosti databáze serveru MySQL můžete vytvořit vysoce dostupné databáze MySQL pomocí podle kroků v této části. 

> [!NOTE]
> Projít tyto kroky na portálu user portal Azure Stack jako uživatel tenanta s předplatným zajišťuje serveru MySQL (Microsoft.MySQLAdapter služby).

1. Přihlaste se k portálu user portal:
    - Nasazení integrovaného systému adresy portálu budou lišit v závislosti na oblasti vašeho řešení a název externí domény. Bude ve formátu https://portal.&lt; *oblast*&gt;.&lt; *Plně kvalifikovaný název domény*&gt;.
    - Pokud používáte Azure Stack Development Kit (ASDK), je uživatelského portálu adresy [ https://portal.local.azurestack.external ](https://portal.local.azurestack.external).

2. Vyberte **\+** **vytvořit prostředek** > **Data \+ úložiště**a potom **databázi MySQL** .<br><br>Zadejte informace o vlastnosti databáze, včetně názvu, řazení, předplatné se má použít a umístění pro nasazení. 

   ![Vytvořit databázi MySQL](./media/azure-stack-tutorial-mysqlrp/createdb1.png)

3. Vyberte **SKU** a pak zvolte příslušné MySQL hostování serveru SKU používat. V tomto příkladu je operátor Azure stacku vytvořil **MySQL-HA** SKU pro podporu vysoké dostupnosti pro cluster databází MySQL.

   ![Vyberte SKU](./media/azure-stack-tutorial-mysqlrp/createdb2.png)

4. Vyberte **přihlášení** > **vytvořte nové přihlašovací údaje** a pak zadejte přihlašovací údaje ověřování MySQL má být použit pro novou databázi. Až budete hotovi, klikněte na tlačítko **OK** a potom **vytvořit** zahájíte proces nasazení databáze.

   ![Přidání přihlášení](./media/azure-stack-tutorial-mysqlrp/createdb3.png)

5. Při nasazení databáze MySQL se úspěšně dokončí, zkontrolujte vlastnosti databáze ke zjištění připojovací řetězec pro připojení k nové databázi s vysokou dostupností. 

   ![Zobrazení připojovacího řetězce](./media/azure-stack-tutorial-mysqlrp/createdb4.png)

## <a name="next-steps"></a>Další postup

V tomto kurzu jste se naučili:

> [!div class="checklist"]
> * Vytvořit cluster MySQL serveru z položky marketplace
> * Vytvoření databáze MySQL Azure Stack, který je hostitelem serveru
> * Vytvoření vysoce dostupné databáze MySQL

Pokračujte k dalším kurzu se dozvíte, jak:
> [!div class="nextstepaction"]
> [Nasazení aplikací do Azure a Azure Stack](azure-stack-solution-pipeline.md)