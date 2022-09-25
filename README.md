## Sample

### (1) Import from Version Catalogs

```groovy
// root/dependencies.gradle

ext {
    // version category로부터 취득
    core = libs.versions.core.get()
    println ">>> Core version : " + core

    // Library 항목들도 category로부터 취득
    def libsFromToml = extensions.getByType(VersionCatalogsExtension).named("libs")
    def librariesValue = [:]
    libsFromToml.libraryAliases.each {
        librariesValue[it] = getLibraryByName(libsFromToml, it)
    }
    libraries = librariesValue
    // check
    println ">>> Libraries in Version Category"
    libraries.each { key, value ->
        println "[${key}] : ${value}"
    }
}

private getLibraryByName(VersionCatalog libs, String name) {
    def library = libs.findLibrary(name)
    if (library.isPresent()) {
        return library.get().get()
    } else {
        throw GradleException("Could not find a library for `$name`")
    }
}
```

Result

```tex
>>> Core version : 1.9.0
>>> Libraries in Version Category
[activity] : androidx.activity:activity-ktx:1.6.0
[androidX.appcompat] : androidx.appcompat:appcompat:1.5.1
[androidX.core] : androidx.core:core:1.9.0
[google.material] : com.google.android.material:material:1.6.1
```

### (2) Use Extra Properties

```groovy
// app/build.gradle

dependencies {
    def ext = rootProject.ext
    // Use version in ExtraPropertiesExtension <-- from Version Category
    implementation "androidx.core:core-ktx:${ext.core}"
    // Use library in ExtraPropertiesExtension <-- from Version Category
    implementation ext.libraries.activity

    // Use library in Version Category
    implementation libs.google.material
}
```

