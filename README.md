# Introduction � Spring MVC

## 0. R�cup�rer le code source du TP

### 0.1. Cloner le d�pot git

> Terminal

```bash
cd /d/idep/Mes\ Documents/eclipse_workspace
git config --global user.name "<Pr�nom Nom>"
git config --global user.email "<email>"
git config --global http.proxy http://proxy-orange.http.insee.fr:8080
git clone https://github.com/Insee-CNIP/formation-spring-mvc.git
git checkout -b tp1b tp1
```

### 0.2. Importer le projet dans Eclipse

> Eclipse

* File
* Import�
* Existing Maven Project
* Root directory : D:\idep\Mes Documents\eclipse_workspace\formation-spring-mvc
* Finish

### 0.3. Cr�er une configuration de lancement

> Eclipse

* Run configuration�
* Maven build > New
* Name : formation-spring-mvc-run
* Base directory : ${workspace_loc:/formation-spring-mvc}
* Goals : clean tomcat7:run -DskipTests
* Apply

## 1. Mise en place

### 1.1. Ajouter les d�pendances Maven

> pom.xml

```xml
<properties>
	<spring.version>4.3.2.RELEASE</spring.version>
</properties>

<dependencies>
	<!-- Spring MVC -->
	<dependency>
		<groupId>org.springframework</groupId>
		<artifactId>spring-webmvc</artifactId>
		<version>${spring.version}</version>
	</dependency>
	
	<!-- Servlet -->
	<dependency>
		<groupId>javax.servlet</groupId>
		<artifactId>javax.servlet-api</artifactId>
		<version>3.1.0</version>
		<scope>provided</scope>
	</dependency>
	<dependency>
		<groupId>javax.servlet.jsp</groupId>
		<artifactId>jsp-api</artifactId>
		<version>2.2</version>
		<scope>provided</scope>
	</dependency>
	<dependency>
		<groupId>jstl</groupId>
		<artifactId>jstl</artifactId>
		<version>1.2</version>
	</dependency>
</dependencies>
```

### 1.2. Cr�er le fichier de contexte de l�application

> src/main/resources/applicationContext.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xmlns:context="http://www.springframework.org/schema/context"
	xsi:schemaLocation="
    http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans-3.0.xsd
    http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context-3.0.xsd
">
	<context:component-scan base-package="fr.insee.bar" />
</beans>
```

### 1.3. Charger le contexte de l�application au d�marage du serveur

> web.xml

```xml
<context-param>
	<param-name>contextConfigLocation</param-name>
	<param-value>classpath:applicationContext.xml</param-value>
</context-param>
<listener>
	<listener-class>org.springframework.web.context.ContextLoaderListener</listener-class>
</listener>
```

### 1.4. Cr�er le fichier de contexte web

> src/main/resources/servlet-dispatcher.xml 

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xmlns:context="http://www.springframework.org/schema/context"
	xmlns:mvc="http://www.springframework.org/schema/mvc"
	xsi:schemaLocation="
    http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans-3.0.xsd
    http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context-3.0.xsd
    http://www.springframework.org/schema/mvc http://www.springframework.org/schema/mvc/spring-mvc.xsd
">
	<context:component-scan base-package="fr.insee.bar" />
</beans>
```

### 1.5. Ajouter la servlet de Spring MVC et diriger toutes les requ�tes vers cette servlet

> web.xml

```xml
<servlet>
	<servlet-name>servlet-dispatcher</servlet-name>
	<servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
	<init-param>
		<param-name>contextConfigLocation</param-name>
		<param-value>classpath:servlet-dispatcher.xml</param-value>
	</init-param>
	<load-on-startup>1</load-on-startup>
</servlet>
<servlet-mapping>
	<servlet-name>servlet-dispatcher</servlet-name>
	<url-pattern>/</url-pattern>
</servlet-mapping>
```

### 1.6. Cr�er le dossier contenant les vues

> src/main/webapp/WEB-INF/views/

### 1.7. D�clarer et param�trer le viewResolver

> servlet-dispatcher.xml

```xml
<bean id="viewResolver"	class="org.springframework.web.servlet.view.InternalResourceViewResolver">
	<property name="prefix">
		<value>WEB-INF/views/</value>
	</property>
	<property name="suffix">
		<value>.jsp</value>
	</property>
</bean>
```

### 1.8. Cr�er le package contenant les contr�leurs

> fr.insee.bar.controller

### 1.9. Cr�er le contr�leur `AccueilController`

> AccueilController.java

Ajouter l�annotation `@Controller`.
Cr�er une m�thode qui dirige vers la vue � accueil.jsp � quand on acc�de � l�URL � /accueil �.
Cette m�thode ajoute au mod�le un objet � message � de type qui vaut "Hello world".
Cr�er la JSP � views/accueil.jsp � et afficher l�objet � message �.

> accueil.jsp

```jsp
<%@ taglib prefix="c" uri="http://java.sun.com/jsp/jstl/core"%>
<h1><c:out value="${message}" /></h1>
```

Tester.

### 1.10. Utiliser un fichier de propri�t�s

> src/main/resources/application-properties

```properties
name=Spring MVC
```

> AccueilController.java

Ajouter l�annotation `@PropertySource("classpath:application.properties")` pour charger le fichier de propri�t�s.
Ajouter un attribute de type `String` dans le contr�leur et l�annoter avec `@Value("${name}")` pour r�cup�rer la valeur de la cl� � name �
Param�trer le message avec cet attribut.
Tester.

### 1.11. Rediriger l�URL racine vers la page d�accueil

> AccueilController.java

Cr�er une nouvelle m�thode qui se d�clenche quand on acc�de � l�URL � / �.
� l�aide de l�instruction `"redirect:/accueil"` rediriger cette URL vers l�URL � /accueil �.
Utiliser un code 301 (redirection permanente) pour effectuer la redirection (important pour le r�f�rencement).
Tester et v�rifier avec les outils de d�veloppement du navigateur que le code est bien 301.