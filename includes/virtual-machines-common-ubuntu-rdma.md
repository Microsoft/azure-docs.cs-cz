1. Instalace dapl, rdmacm, ibverbs a mlx4

  ```bash
  sudo apt-get update

  sudo apt-get install libdapl2 libmlx4-1

  ```

2. V /etc/waagent.conf povolte RDMA pomocí uncommenting následující řádky konfigurace. Potřebujete kořenový přístup k úpravě tohoto souboru.
  
  ```
  OS.EnableRDMA=y

  OS.UpdateRdmaDriver=y
  ```

3. Přidat nebo změnit následující nastavení paměti v KB /etc/security/limits.conf souboru. Potřebujete kořenový přístup k úpravě tohoto souboru. Pro účely testování můžete nastavit memlock na neomezený. Například: `<User or group name>   hard    memlock   unlimited`.

  ```
  <User or group name> hard    memlock <memory required for your application in KB>

  <User or group name> soft    memlock <memory required for your application in KB>
  ```
  
4. Nainstalujte knihovnu Intel MPI. Buď [zakoupit a stáhnout](https://software.intel.com/intel-mpi-library/) knihovnu z Intel nebo ke stažení [bezplatné zkušební verze](https://registrationcenter.intel.com/en/forms/?productid=1740).

  ```bash
  wget http://registrationcenter-download.intel.com/akdlm/irc_nas/tec/9278/l_mpi_p_5.1.3.223.tgz
  ```
 
 Jsou podporovány pouze moduly runtime 5.x Intel MPI.
 
 Postup instalace najdete v tématu [Intel MPI knihovny instalační příručce](http://registrationcenter-download.intel.com/akdlm/irc_nas/1718/INSTALL.html?lang=en&fileExt=.html).

5. Povolte ptrace pro nekořenovými procesy mimo ladicí program (třeba pro nejnovější verze Intel MPI).
 
  ```bash
  echo 0 | sudo tee /proc/sys/kernel/yama/ptrace_scope
  ```
