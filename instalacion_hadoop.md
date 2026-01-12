# Instalación de Hadoop en VirtualBox

Este documento describe paso a paso cómo instalar **Apache Hadoop** en una máquina virtual creada con **VirtualBox**, utilizando **Ubuntu** como sistema operativo invitado. Incluye explicación y comandos listos para ejecutar.

---

## 1. Preparar el sistema en VirtualBox
Primero se crea una máquina virtual en VirtualBox con Ubuntu (Server o Desktop).  
Es recomendable asignar al menos **4 GB de RAM**, **2 núcleos de CPU** y configurar la red en **modo puente o NAT** para permitir la comunicación.  
Esto asegura que Hadoop tenga los recursos mínimos para funcionar.

---

## 2. Instalar Java
Hadoop está desarrollado en **Java**, por lo que es necesario instalarlo y configurar las variables de entorno.

```bash
sudo apt update
sudo apt install openjdk-8-jdk -y
#Comprueb instalación
sudo update-alternatives --config java 
java -version

echo "export JAVA_HOME=/usr/lib/jvm/java-8-openjdk-amd64" >> ~/.bashrc
echo "export PATH=$PATH:$JAVA_HOME/bin" >> ~/.bashrc
source ~/.bashrc
```

---

## 3. Crear usuario para Hadoop y configurar SSH

```bash
sudo adduser hduser
sudo usermod -aG sudo hduser
su - hduser
sudo apt install openssh-server -y
ssh-keygen -t rsa -P ""
cat ~/.ssh/id_rsa.pub >> ~/.ssh/authorized_keys
chmod 600 ~/.ssh/authorized_keys
ssh localhost
```

---

## 4. Descargar e instalar Hadoop

```bash
wget https://downloads.apache.org/hadoop/common/hadoop-3.3.6/hadoop-3.3.6.tar.gz
sudo tar -xvzf hadoop-3.3.6.tar.gz -C /usr/local/
sudo mv /usr/local/hadoop-3.3.6 /usr/local/hadoop
sudo chown -R hduser:hduser /usr/local/hadoop
echo "export HADOOP_HOME=/usr/local/hadoop" >> ~/.bashrc
echo "export HADOOP_INSTALL=$HADOOP_HOME" >> ~/.bashrc
echo "export HADOOP_MAPRED_HOME=$HADOOP_HOME" >> ~/.bashrc
echo "export HADOOP_COMMON_HOME=$HADOOP_HOME" >> ~/.bashrc
echo "export HADOOP_HDFS_HOME=$HADOOP_HOME" >> ~/.bashrc
echo "export YARN_HOME=$HADOOP_HOME" >> ~/.bashrc
echo "export PATH=$PATH:$HADOOP_HOME/bin:$HADOOP_HOME/sbin" >> ~/.bashrc
source ~/.bashrc
```

---

## 5. Configuración de Hadoop

### core-site.xml
```xml
<configuration>
  <property>
    <name>fs.defaultFS</name>
    <value>hdfs://localhost:9000</value>
  </property>
</configuration>
```

### hdfs-site.xml
```xml
<configuration>
  <property>
    <name>dfs.replication</name>
    <value>1</value>
  </property>
</configuration>
```

### mapred-site.xml
```xml
<configuration>
  <property>
    <name>mapreduce.framework.name</name>
    <value>yarn</value>
  </property>
</configuration>
```

### yarn-site.xml
```xml
<configuration>
  <property>
    <name>yarn.nodemanager.aux-services</name>
    <value>mapreduce_shuffle</value>
  </property>
</configuration>
```

---

## 6. Formatear el NameNode

```bash
hdfs namenode -format
```

---

## 7. Iniciar Hadoop

```bash
start-dfs.sh
start-yarn.sh
jps
```

Se deberían ver los procesos: **NameNode**, **DataNode**, **ResourceManager**, **NodeManager**.

---

## 8. Acceso a las interfaces web

- **HDFS** → http://localhost:9870
- **YARN** → http://localhost:8088

---

✅ Con esto ya tendrás un entorno Hadoop de **nodo único** funcionando dentro de VirtualBox, listo para pruebas.
