---
title: Zásady podpory clusteru Azure Red Hat OpenShift 4
description: Principy požadavků zásad podpory pro Red Hat OpenShift 4
author: sakthi-vetrivel
ms.author: suvetriv
ms.service: container-service
ms.topic: conceptual
ms.date: 11/23/2020
ms.openlocfilehash: c8d52609043f173e896668eadeb8c59493739859
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/24/2020
ms.locfileid: "95521134"
---
# <a name="azure-red-hat-openshift-support-policy"></a>Zásady podpory Azure Red Hat OpenShift

Některé konfigurace pro clustery Azure Red Hat OpenShift 4 můžou ovlivnit možnost podpory clusteru. Azure Red Hat OpenShift 4 umožňuje správcům clusteru provádět změny v interních součástech clusteru, ale ne všechny změny se podporují. Níže uvedené zásady podpory sdílí, které úpravy porušují zásady, a zamění podporu od Microsoftu a Red Hat.

> [!NOTE]
> Funkce s označením Technology Preview v platformě kontejneru OpenShift se v Azure Red Hat OpenShift nepodporují.

## <a name="cluster-configuration-requirements"></a>Požadavky na konfiguraci clusteru

* Všechny obsluhy clusteru OpenShift musí zůstat ve spravovaném stavu. Seznam operátorů clusteru lze vrátit spuštěním `oc get clusteroperators` .
* Cluster musí mít minimálně dva pracovní uzly. Neměňte škálu pracovních procesů clusteru na nulu nebo se pokuste o bezproblémové vypnutí clusteru.
* Neodstraňujte ani neměňte cluster Prometheus a služby Alertmanager.
* Neodstraňujte pravidla Alertmanager služby.
* Neodstraňujte ani neměňte protokolování služby Azure Red Hat OpenShift (MDSD lusky).
* Neodstraňujte ani neupravujte tajný klíč pro vyžádání obsahu clusteru arosvc.azurecr.io.
* Všechny virtuální počítače s clustery musí mít přímý odchozí přístup k Internetu, přinejmenším k koncovým bodům Azure Resource Manager (ARM) a Service Logging (Ženeva).  Není podporována žádná forma proxy serveru HTTPS.
* Neměňte konfiguraci DNS pro virtuální síť clusteru. Je nutné použít výchozí překladač Azure DNS.
* Nepřepisujte žádné objekty MachineConfig clusteru (například konfigurace kubelet) jakýmkoli způsobem.
* Nenastavte žádné možnosti unsupportedConfigOverrides. Nastavením těchto možností zabráníte upgradu dílčí verze.
* Služba Azure Red Hat OpenShift přistupuje ke clusteru prostřednictvím služby privátního propojení.  Neodstraňujte ani neměňte přístup k službě.
* Výpočetní uzly, které nejsou RHCOS, se nepodporují. Například nemůžete použít výpočetní uzel RHEL.

## <a name="supported-virtual-machine-sizes"></a>Podporované velikosti virtuálních počítačů

Azure Red Hat OpenShift 4 podporuje instance pracovních uzlů na následujících velikostech virtuálních počítačů:

### <a name="general-purpose"></a>Obecné účely

|Řada|Velikost|Virtuální procesory|Paměť: GiB|
|-|-|-|-|
|Dasv4|Standard_D4as_v4|4|16|
|Dasv4|Standard_D8as_v4|8|32|
|Dasv4|Standard_D16as_v4|16|64|
|Dasv4|Standard_D32as_v4|32|128|
|Dsv3|Standard_D4s_v3|4|16|
|Dsv3|Standard_D8s_v3|8|32|
|Dsv3|Standard_D16s_v3|16|64|
|Dsv3|Standard_D32s_v3|32|128|

### <a name="memory-optimized"></a>Optimalizované z hlediska paměti

|Řada|Velikost|Virtuální procesory|Paměť: GiB|
|-|-|-|-|
|Esv3|Standard_E4s_v3|4|32|
|Esv3|Standard_E8s_v3|8|64|
|Esv3|Standard_E16s_v3|16|128|
|Esv3|Standard_E32s_v3|32|256|

### <a name="compute-optimized"></a>Optimalizované z hlediska výpočetních služeb

|Řada|Velikost|Virtuální procesory|Paměť: GiB|
|-|-|-|-|
|Fsv2|Standard_F4s_v2|4|8|
|Fsv2|Standard_F8s_v2|8|16|
|Fsv2|Standard_F16s_v2|16|32|
|Fsv2|Standard_F32s_v2|32|64|

### <a name="master-nodes"></a>Hlavní uzly

|Řada|Velikost|Virtuální procesory|Paměť: GiB|
|-|-|-|-|
|Dsv3|Standard_D8s_v3|8|32|
|Dsv3|Standard_D16s_v3|16|64|
|Dsv3|Standard_D32s_v3|32|128|
