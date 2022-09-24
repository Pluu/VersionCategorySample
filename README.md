## Sample

### (1) Import from Version Catalogs

```groovy
// root/dependencies.gradle

ext {
    def libs = extensions.getByType(VersionCatalogsExtension).named("libs")

    // version category로부터 취득
    core = libs.findVersion("core").get()

    // Library 항목들도 category로부터 취득
    def librariesValue = [:]
    libs.libraryAliases.each {
        librariesValue[it] = getLibraryByName(libs, it)
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
    implementation libs.androidX.activity.ktx
}
```

