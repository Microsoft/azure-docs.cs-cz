---
title: Porovnání různých typů testovacích prostředí v Azure Lab Services | Microsoft Docs
description: Popisuje a porovnává různé typy testovacích prostředí, které můžete vytvořit pomocí Azure Lab Services (dříve DevTest Labs).
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 04/30/2018
ms.author: spelluru
ms.openlocfilehash: 3b2fd59444f50f4ac5da6bf569cb0247f3a36943
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/07/2018
---
# <a name="compare-managed-and-custom-labs-in-azure-lab-services"></a>Porovnání spravovaných a vlastních testovacích prostředí v Azure Lab Services
Je možné vytvořit dva typy testovacích prostředí: **spravovaná testovací prostředí** v Azure Lab Services a **vlastní testovací prostředí** v Azure DevTest Labs. Pokud chcete jenom určit, co v testovacím prostředí potřebujete, a nechat příslušnou službu nastavit a spravovat infrastrukturu potřebnou pro dané testovací prostředí, vyberte některé **spravované testovací prostředí**. V současné době je jediným typem spravovaného testovacího prostředí, které lze vytvořit pomocí Azure Lab Services, **testovací prostředí v učebně**. Pokud chcete spravovat vlastní infrastrukturu, vytvořte pomocí Azure DevTest Labs **vlastní testovací prostředí**.

Následující části poskytují další podrobnosti o těchto testovacích prostředích. 

## <a name="managed-labs"></a>Spravovaná testovací prostředí
Spravovaná testovací prostředí nabízejí různé typy testovacích prostředí, které jsou vhodné pro specifické potřeby. V současné době služba Azure Lab Services podporuje jako spravované testovací prostředí jenom **testovací prostředí v učebně**. Spravovaná testovací prostředí vyžadují minimální nastavení a umožňují okamžité zahájení práce. Samotná služba zajišťuje veškerou správu infrastruktury pro testovací prostředí, od poskytování potřebného výkonu virtuálních počítačů přes zpracovávání chyb až po škálování infrastruktury. Pokud chcete vytvořit spravované testovací prostředí, je nutné nejprve vytvořit účet testovacího prostředí pro vaši organizaci. Účet testovacího prostředí slouží jako centrální účet, ve kterém se spravují všechna testovací prostředí v organizaci. 

Při vytváření a používání prostředků Azure v těchto spravovaných prostředích služba vytváří a spravuje prostředky v interních předplatných Microsoftu. Nevytvářejí se ve vašem vlastním předplatném Azure. Služba uchovává informace o využití těchto prostředků v interních předplatných Microsoftu. Toto využití se fakturuje zpět na vaše předplatné Azure, které obsahuje účet testovacího prostředí.   

Tady je několik **případů použití pro spravovaná testovací prostředí**: 

- Poskytněte studentům testovací prostředí s virtuálními počítači, které jsou nakonfigurované přesně tak, jak je pro vyučování potřeba. Můžete pro studenty omezit používání virtuálních počítačů pro domácí úkoly nebo osobní projekty na určitý počet hodin.
- Nastavte fond virtuálních počítačů s vysokým výpočetním výkonem tak, aby bylo možné provádět výzkum náročný na výpočetní výkon nebo na výkon grafiky. Spouštějte virtuální počítače podle potřeby a po dokončení tyto počítače vyčistěte. 
- Přesuňte fyzickou počítačovou učebnu ve vaší škole do cloudu. Automaticky škálujte počet virtuálních počítačů tak, aby nedošlo k překročení limitů využití a nákladů, které jste pro testovací prostředí nastavili.  
- Vytvořte rychle testovací prostředí s virtuálními počítači pro hostování hackathonu. Jakmile testovací prostředí přestanete potřebovat, můžete ho jedním kliknutím odstranit. 


## <a name="custom-labs"></a>Vlastní testovací prostředí
Můžete mít scénáře, ve kterých chcete spravovat veškerou infrastrukturu a konfiguraci sami v rámci svého vlastního předplatného. Uděláte to tak, že vytvoříte vlastní testovací prostředí pomocí služby Azure DevTest Labs na webu Azure Portal. Pro tato testovací prostředí není nutné vytvořit účet testovacího prostředí. Tato testovací prostředí se nezobrazují v účtu testovacího prostředí (který se používá pro spravovaná testovací prostředí).  

Tady je několik **případů použití pro vlastní testovací prostředí**: 

- Vytvořte rychle testovací prostředí s virtuálními počítači pro hostování hackathonu nebo praktické relace na konferenci. Jakmile testovací prostředí přestanete potřebovat, můžete ho jedním kliknutím odstranit. 
- Vytvořte fond virtuálních počítačů, ve kterých je nakonfigurovaná vaše aplikace, a umožněte vašemu týmu použít virtuální počítač pro společné hledání chyb vývojáři.  
- Poskytněte vývojářům virtuální počítače, ve kterých jsou nakonfigurované všechny potřebné nástroje. Naplánujte automatické spouštění a vypínání, aby se minimalizovaly náklady. 
- Vytvořte v rámci vašeho nasazení opakovaně testovací prostředí s testovacími počítači. Otestujte váš nejnovější kód a po dokončení testovací počítače vyčistěte. 
- Nastavte řadu různě nakonfigurovaných virtuálních počítačů a testovacích agentů pro škálování a testování výkonu. 
- Nabídněte vašim zákazníkům školení s využitím testovacího prostředí, ve kterém je nakonfigurovaná nejnovější verze vašeho produktu. Poskytněte každému zákazníkovi určitý počet hodin pro práci v testovacím prostředí. 


## <a name="managed-labs-vs-custom-labs"></a>Porovnání spravovaných a vlastních testovacích prostředí
Následující tabulka porovnává dva typy testovacích prostředí, které služba Azure Lab Services podporuje: 

| Funkce | Spravovaná testovací prostředí | Vlastní testovací prostředí |
| -------- | ----------------  | ---------- |
| Správa infrastruktury Azure v testovacím prostředí |  Automaticky spravuje služba. | Spravujete sami.  |
| Předdefinovaná odolnost vůči problémům s infrastrukturou | Automaticky zpracovává služba. | Spravujete sami.  |
| Správa předplatného | Služba zpracovává přidělování prostředků v předplatných Microsoftu, která podporují danou službu. Škálování automaticky zpracovává služba. | Spravujete sami ve svém vlastním předplatném Azure. Bez automatického škálování předplatných. |
| Nasazení Azure Resource Manageru v testovacím prostředí | Není k dispozici. | K dispozici. |

## <a name="next-steps"></a>Další kroky
Začínáme s nastavením testovacího prostředí pomocí Azure Lab Services:

- [Nastavení testovacího prostředí v učebně](tutorial-setup-classroom-lab.md)
- [Nastavení vlastního testovacího prostředí](tutorial-create-custom-lab.md)
