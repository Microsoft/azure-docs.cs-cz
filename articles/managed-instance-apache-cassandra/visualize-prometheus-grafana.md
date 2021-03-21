---
title: Konfigurace Grafana pro vizualizaci metriky vydávané ze spravované instance Azure pro Apache Cassandra
description: Naučte se, jak nainstalovat a nakonfigurovat Grafana ve virtuálním počítači, abyste mohli vizualizovat metriky vydávané ze spravované instance Azure pro cluster Apache Cassandra.
author: TheovanKraay
ms.service: managed-instance-apache-cassandra
ms.topic: how-to
ms.date: 03/02/2021
ms.author: thvankra
ms.openlocfilehash: ed0ff343595429a4cb81fef280203f1180eeb098
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "101744828"
---
# <a name="configure-grafana-to-visualize-metrics-emitted-from-the-managed-instance-cluster"></a>Konfigurace Grafana pro vizualizaci metriky vypouštěné z clusteru spravované instance

> [!IMPORTANT]
> Spravovaná instance Azure pro Apache Cassandra je aktuálně ve verzi Public Preview.
> Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro úlohy v produkčním prostředí. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti.
> Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Když nasadíte spravovanou instanci Azure pro cluster Apache Cassandra, služba zřídí server, který je hostitelem [Prometheus](https://prometheus.io/) , který můžou využívat různé nástroje klienta. Prometheus je open source monitorovací řešení. Spravovaná instance vygeneruje metriky a zachová 10 minut nebo 10 GB dat (podle toho, jaká prahová hodnota dosáhne prvního). Tento článek popisuje, jak nakonfigurovat Grafana k vizualizaci metrik emitovaných z clusteru Managed instance. K vizualizaci metrik se vyžadují tyto úkoly:

* Nasaďte virtuální počítač s Ubuntu do Azure Virtual Network, kde se nachází spravovaná instance.
* Nainstalujte open source [Nástroj Grafana](https://grafana.com/grafana/) a Sestavujte řídicí panely a vizualizujte metriky vydávané z Prometheus.

## <a name="deploy-a-ubuntu-server"></a>Nasazení serveru Ubuntu

1. Přihlaste se na [Azure Portal](https://portal.azure.com/).

1. Přejděte do skupiny prostředků, ve které se nachází cluster spravované instance. Vyberte **Přidat** a vyhledejte image **Ubuntu serveru 18,04 LTS** :

   :::image type="content" source="./media/visualize-prometheus-grafana/select-ubuntu-image.png" alt-text="Najde Ubuntu image serveru z Azure Portal." border="true":::

1. Vyberte image a vyberte **vytvořit**.

1. V okně **vytvořit virtuální počítač** zadejte hodnoty pro následující pole, kde můžete ponechat výchozí hodnoty pro další pole:

   * **Název virtuálního počítače** – zadejte název vašeho virtuálního počítače.
   * **Oblast** – vyberte stejnou oblast, do které jste nasadili Virtual Network.

   :::image type="content" source="./media/visualize-prometheus-grafana/create-vm-ubuntu.png" alt-text="Vyplňte formulář a vytvořte virtuální počítač s imagí Ubuntu serveru." border="true":::

1. Na kartě **sítě** vyberte Virtual Network, ve které je nasazená vaše spravovaná instance:

   :::image type="content" source="./media/visualize-prometheus-grafana/configure-networking-details.png" alt-text="Nakonfigurujte nastavení sítě Ubuntu serveru." border="true":::

1. Nakonec vyberte **zkontrolovat + vytvořit** a vytvořte server Grafana.

## <a name="install-grafana"></a>Nainstalovat Grafana

1. Z Azure Portal otevřete Virtual Network, kde jste nasadili spravovanou instanci a server Grafana. Měla by se zobrazit instance sady škálování virtuálního počítače s názvem **Cassandra-skok (instance 0)**. Tato Prometheus metriky se hostují v této sadě škálování virtuálních počítačů. Poznamenejte si IP adresu této instance:

   :::image type="content" source="./media/visualize-prometheus-grafana/prometheus-instance-address.png" alt-text="Získá IP adresu instance Prometheus." border="true":::

1. Připojte se k nově vytvořenému serveru Ubuntu pomocí rozhraní příkazového [řádku Azure](../virtual-machines/linux/ssh-from-windows.md#ssh-clients) nebo preferovaného klientského nástroje, abyste se připojili přes SSH.

1. Po připojení k VIRTUÁLNÍmu počítači musíte nainstalovat a nakonfigurovat Grafana pro připojení k sadě škálování virtuálního počítače, kde jsou tyto metriky hostované. Otevřete příkazový řádek a zadáním `nano` příkazu otevřete textový editor nano. Vložte následující skript do textového editoru, nezapomeňte nahradit `<prometheus IP address>` adresu IP, kterou jste si poznamenali v předchozím kroku:

   ```bash
   #!/bin/bash
   
   echo "Installing Grafana..."
   
   if ! $SSH dpkg -s grafana prometheus > /dev/null; then
       echo "Installing packages."
       echo 'deb https://packages.grafana.com/oss/deb stable main' | $SSH sudo tee /etc/apt/sources.list.d/grafana.list > /dev/null
       curl https://packages.grafana.com/gpg.key | $SSH sudo apt-key add -
       $SSH sudo apt-get update
       $SSH sudo apt-get install -y grafana prometheus
   else
       echo "Skipping package installation"
   fi
   
   echo "Configuring grafana"
   cat <<EOF | $SSH sudo tee /etc/grafana/provisioning/datasources/prometheus.yml
   apiVersion: 1
   datasources:
     - name: Prometheus
       type: prometheus
       url: https://<prometheus IP address>:9443
       jsonData:
         tlsSkipVerify: true
   EOF
   
   echo "Restarting Grafana"
   $SSH sudo systemctl enable grafana-server
   $SSH sudo systemctl restart grafana-server
   
   echo "Installing Grafana plugins"
   $SSH sudo grafana-cli plugins install natel-discrete-panel
   $SSH sudo grafana-cli plugins install grafana-polystat-panel
   $SSH sudo systemctl restart grafana-server
   ```

1. Zadejte `ctrl + X` a uložte soubor. Soubor můžete pojmenovat `grafana.sh` .

1. `./grafana.sh`Do příkazového řádku zadejte příkaz pro instalaci Grafana.

1. Po dokončení instalace bude Grafana k dispozici na **portu 3000** na IP adrese serveru, jak je znázorněno na následujícím snímku obrazovky:

   :::image type="content" source="./media/visualize-prometheus-grafana/open-grafana-port.png" alt-text="Spusťte Grafana na portu 3000." border="true":::

1. Můžete si vybrat z open source řídicích panelů vytvořených pro Apache Cassandra v Grafana, jako je například soubor JSON [s přehledem clusteru](https://github.com/TheovanKraay/cassandra-exporter/blob/master/grafana/instaclustr/cluster-overview.json) . Stáhněte a importujte definici JSON řídicího panelu do Grafana:

   :::image type="content" source="./media/visualize-prometheus-grafana/grafana-import.png" alt-text="Importujte definici JSON pro Grafana." border="true":::

   :::image type="content" source="./media/visualize-prometheus-grafana/grafana-upload-json.png" alt-text="Nahrajte definici JSON pro Grafana." border="true":::

1. Následně můžete monitorovat cluster spravovaných instancí Cassandra s vybraným řídicím panelem:

   :::image type="content" source="./media/visualize-prometheus-grafana/monitor-cassandra-metrics.gif" alt-text="Zobrazte metriky spravované instance Cassandra na řídicím panelu." border="true":::

## <a name="next-steps"></a>Další kroky

V tomto článku jste zjistili, jak nakonfigurovat řídicí panely pro vizualizaci metrik v Prometheus pomocí Grafana. Další informace o spravované instanci Azure pro Apache Cassandra najdete v těchto článcích:

* [Přehled služby Azure Managed instance pro Apache Cassandra](introduction.md)
* [Nasazení clusteru spravovaného Apache Spark s využitím Azure Databricks (Preview)](deploy-cluster-databricks.md)
