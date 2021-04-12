---
title: Běžné chyby a známé problémy při migraci do Azure Cloud Services (Rozšířená podpora)
description: Přehled běžných chyb při migraci z Cloud Services (Classic) do cloudové služby (Rozšířená podpora)
ms.topic: how-to
ms.service: cloud-services-extended-support
ms.subservice: classic-to-arm-migration
author: tanmaygore
ms.author: tagore
ms.reviewer: mimckitt
ms.date: 2/08/2021
ms.custom: ''
ms.openlocfilehash: 58203730793202649c401d96182469fa1eac6ef1
ms.sourcegitcommit: b8995b7dafe6ee4b8c3c2b0c759b874dff74d96f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/03/2021
ms.locfileid: "106286881"
---
# <a name="common-errors-and-known-issues-when-migration-to-azure-cloud-services-extended-support"></a>Běžné chyby a známé problémy při migraci do Azure Cloud Services (Rozšířená podpora)

Tento článek popisuje známé problémy a běžné chyby, se kterými se můžete setkat při migraci z Cloud Services (Classic) na Cloud Services (Rozšířená podpora). 

## <a name="known-issues"></a>Známé problémy
Následující problémy jsou známé a řeší se.

| Známé problémy | Omezení rizik | 
|---|---|
| Instance rolí restartují UD by UD po úspěšném potvrzení. | Operace restartu se řídí stejnou metodou jako měsíc uvádění hostovaného operačního systému. Neprovádějte migraci cloudových služeb s jednou instancí role nebo s tím vlivem na restartování.| 
| Po obnovení prohlížeče se Azure Portal nedá přečíst stav migrace. | Znovu spusťte operaci ověřování a příprava pro návrat k původnímu stavu migrace. | 
| Certifikát zobrazený jako prostředek tajného klíče v trezoru klíčů | Po migraci znovu Nahrajte certifikát jako prostředek certifikátu, aby se zjednodušila operace aktualizace na Cloud Services (Rozšířená podpora). | 
| Štítky nasazení nejsou v rámci migrace uloženy jako značky. | Po migraci pro zachování těchto informací ručně vytvořte značky.
| Název skupiny prostředků je ve všech velkých písmenech. | Bez dopadu. Řešení ještě není k dispozici. |
| Název zámku při Cloud Services (Rozšířená podpora) je nesprávný. | Bez dopadu. Řešení ještě není k dispozici. | 
| V okně portálu pro Cloud Services (Rozšířená podpora) je název IP adresy nesprávný. | Bez dopadu. Řešení ještě není k dispozici. | 
| Po operaci aktualizace u migrované cloudové služby se zobrazuje neplatný název DNS pro virtuální IP adresu. | Bez dopadu. Řešení ještě není k dispozici. | 
| Po úspěšné přípravě se nepovoluje propojení nového nasazení Cloud Services (Rozšířená podpora) jako zaměnitelné. | Neodkazujte novou cloudovou službu jako vyměněnou na připravenou cloudovou službu. | 
| Chybové zprávy se musí aktualizovat. | Bez dopadu. | 

## <a name="common-migration-errors"></a>Běžné chyby při migraci
Běžné chyby migrace a kroky pro zmírnění rizik. 

| Chybová zpráva | Podrobnosti | 
|---|---|
| Typ prostředku se nepovedlo najít v oboru názvů `Microsoft.Compute` pro rozhraní API verze 2020-10-01-Preview. | [Zaregistrujte](in-place-migration-overview.md#setup-access-for-migration) příznak funkce předplatné pro funkci CloudServices pro přístup k verzi Public Preview. | 
| Došlo k vnitřní chybě serveru. Opakujte požadavek. | Zkuste operaci zopakovat, použijte [Microsoft Q&a](https://docs.microsoft.com/answers/topics/azure-cloud-services-extended-support.html) nebo kontaktujte podporu. | 
| Při pokusu o přidělení síťových prostředků pro cloudovou službu došlo k neočekávané chybě serveru. Opakujte požadavek. | Zkuste operaci zopakovat, použijte [Microsoft Q&a](https://docs.microsoft.com/answers/topics/azure-cloud-services-extended-support.html) nebo kontaktujte podporu. | 
| Nasazení nasazení – název v cloudové službě cloud – služba-název musí být v rámci virtuální sítě, která se má migrovat. | Nasazení se nenachází ve virtuální síti. Další podrobnosti najdete v [tomto](in-place-migration-technical-details.md#migration-of-deployments-not-in-a-virtual-network) dokumentu. | 
| Migrace nasazení nasazení – název v cloudové službě cloud-služba – název není podporovaný, protože je v oblasti názvu oblasti. Povolené oblasti: [seznam oblastí, které jsou k dispozici]. | Oblast zatím není pro migraci podporovaná. | 
| Nasazení nasazení – název v cloudové službě cloudové služby nelze migrovat, protože k rolím s názvem role (y) nejsou přidruženy žádné podsítě. Přidružte všechny role k podsíti a pak zkuste migrovat cloudovou službu znovu. | Aktualizujte nasazení cloudové služby (Classic) tak, že ho před migrací umístíte do podsítě. |  
| Nasazení nasazení – název v cloudové službě cloudové služby nejde migrovat, protože nasazení vyžaduje aspoň jednu funkci, která není zaregistrovaná v předplatném v Azure Resource Manager. Zaregistrujte všechny požadované funkce pro migraci tohoto nasazení. Chybějící funkce: [seznam chybějících funkcí]. | Pokud chcete získat zaregistrované příznaky funkcí, obraťte se na podporu. | 
| Nasazení nelze migrovat, protože cloudová služba nasazení má dva obsazené sloty. Migrace cloudových služeb se podporuje jenom pro nasazení, která jsou jediným nasazením v cloudové službě. Pokud chcete pokračovat v migraci tohoto nasazení, odstraňte druhé nasazení v cloudové službě. | Další podrobnosti najdete v seznamu [nepodporovaných scénářů](in-place-migration-overview.md#unsupported-configurations--migration-scenarios) . | 
| Nasazení nasazení – název ve hostované službě Cloud-Service-Name je v přechodném stavu: State. Migrace není povolena. | Nasazení je buď vytvořeno, odstraněno nebo aktualizováno. Počkejte, až se operace dokončí, a zkuste to znovu. | 
| Nasazení nasazení – název v hostované službě Cloud-Service-Name má rezervované IP adresy, ale žádný rezervovaný název IP. Pokud chcete tento problém vyřešit, aktualizujte název rezervované IP adresy nebo se obraťte na oddělení služby Microsoft Azure. | Aktualizujte nasazení cloudové služby. | 
| Nasazení nasazení – název v hostované službě Cloud-Service-Name má rezervované IP adresy rezervované IP adresy, ale žádný koncový bod na rezervované IP adrese. Chcete-li tento problém vyřešit, přidejte alespoň jeden koncový bod do rezervované IP adresy. | Přidejte koncový bod do rezervované IP adresy. | 
| Migrace nasazení {0} v nástroji hostované službě {1} se potvrzuje a nedá se změnit, dokud se úspěšně nedokončí.  | Počkejte nebo zkuste operaci zopakovat. | 
| Migrace nasazení {0} v nástroji hostované službě {1} se přerušila a nedá se změnit, dokud se úspěšně nedokončí. | Počkejte nebo zkuste operaci zopakovat. |
| Jeden nebo více virtuálních počítačů v nasazení {0} v hostované službě {1} provádí operaci aktualizace. Nedá se migrovat, dokud se předchozí operace úspěšně nedokončí. Zkuste to znovu po nějaké době. | Počkejte na dokončení operace. | 
| Migrace není pro nasazení {0} v hostované službě podporovaná {1} , protože používá následující funkce, které se zatím nepodporují pro migraci: nasazení mimo virtuální síť.| Nasazení se nenachází ve virtuální síti. Další podrobnosti najdete v [tomto](in-place-migration-technical-details.md#migration-of-deployments-not-in-a-virtual-network) dokumentu. | 
| Název virtuální sítě nemůže být null nebo prázdný. | Zadat název virtuální sítě v textu žádosti REST | 
| Název podsítě nemůže být null nebo prázdný. | Zadejte název podsítě v textu žádosti REST. | 
| DestinationVirtualNetwork musí být nastavená na jednu z následujících hodnot: Default, New nebo Existovaná. | V textu žádosti REST zadejte vlastnost DestinationVirtualNetwork. | 
| Výchozí možnost cíle virtuální sítě není implementovaná. | Hodnota default není v textu žádosti REST podporována pro vlastnost DestinationVirtualNetwork. | 
| Nasazení {0} nelze migrovat, protože CSPKG není k dispozici. | Upgradujte nasazení a zkuste to znovu. | 
| Podsíť s ID {0} se nachází v jiném umístění než nasazení {1} v hostované službě {2} . Umístění pro podsíť je ' {3} ' a umístění pro hostovanou službu je ' {4} '.  Zadejte podsíť ve stejném umístění jako nasazení. | Aktualizujte cloudovou službu tak, aby měla stejnou podsíť i cloudovou službu ve stejném umístění před migrací. | 
| Migrace nasazení {0} v nástroji hostované službě {1} se přerušila a nedá se změnit, dokud se úspěšně nedokončí. | Počkejte na dokončení přerušení nebo zkuste operaci zrušit. Použijte [Microsoft Q&a nebo se obraťte na](https://docs.microsoft.com/answers/topics/azure-cloud-services-extended-support.html) podporu jinak. | 
| Nasazení {0} v hostované službě nebylo {1} připraveno k migraci. | Před spuštěním operace potvrzení spusťte přípravu na cloudovou službu. | 
| UnknownExceptionInEndExecute: kontrakt. Assert se nezdařil: rgName má hodnotu null nebo je prázdný: výjimka byla přijata v EndExecute, která není RdfeException. |   Použijte [Microsoft Q&a](https://docs.microsoft.com/answers/topics/azure-cloud-services-extended-support.html) nebo kontaktujte podporu. | 
| UnknownExceptionInEndExecute: úloha byla zrušena: byla přijata výjimka v EndExecute, která není RdfeException. | Použijte [Microsoft Q&a](https://docs.microsoft.com/answers/topics/azure-cloud-services-extended-support.html) nebo kontaktujte podporu. | 
| XrpVirtualNetworkMigrationError: Chyba migrace virtuální sítě. | Použijte [Microsoft Q&a](https://docs.microsoft.com/answers/topics/azure-cloud-services-extended-support.html) nebo kontaktujte podporu. | 
| Nasazení {0} v hostované službě {1}  patří do Virtual Network {2} . Migrujte Virtual Network {2} pro migraci tohoto hostované službě {1} . | Přečtěte si [Virtual Network migrace](in-place-migration-technical-details.md#virtual-network-migration). | 
| Aktuální kvóta pro název prostředku v Azure Resource Manager není dostatečná pro dokončení migrace. Aktuální kvóta je {0} , další potřeba je {1} . Požádejte o podporu žádosti o zvýšení kvóty a opakujte migraci, jakmile se kvóta vygeneruje.    | Dodržujte vhodné kanály a vyžádejte si zvýšení kvóty: <br>[Zvýšení kvóty pro síťové prostředky](../azure-portal/supportability/networking-quota-requests.md) <br>[Zvýšení kvóty pro výpočetní prostředky](../azure-portal/supportability/per-vm-quota-requests.md) | 

## <a name="next-steps"></a>Další kroky
Další informace o požadavcích migrace najdete v tématu [technické podrobnosti o migraci na Azure Cloud Services (Rozšířená podpora)](in-place-migration-technical-details.md) .
