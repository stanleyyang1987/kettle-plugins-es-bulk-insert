# kettle-plugins-es-bulk-insert
support for elasticsearch 6.3.0

I have down kettle for version 8.1.0.1 and  hacking for  the [ elasticsearch bulk insert ] plugin for support  the newest elasticsearch 6.3.0.

It works  on Pentaho Data Intergation 7.0.0 .

I can not upload  the final zip file  because  of github 25MB file size limit.
So I post the compile proccess
1. One  must do some changing on the maven settings.xml accouding to url
   https://raw.githubusercontent.com/pentaho/maven-parent-poms/master/maven-support-files/settings.xml
   By the way,the pluginGroups tag in the settings.xml is deprecated for my maven 3.3.9,so just remove it.

2. After importing my project ,run command:
   mvn clean package -Dmaven.test.skip  
   you will get the zip file under the assemble directory after a long running time

3. unzip the file. and copy the elasticsearch-bulk-insert-plugin to the kettle plugins directory

4. elasticsearch 6.3.0 does not support the kettle BigNumber type.So if  you encount the exception:
   "cannot write xcontent for unknown value of type for java.math.BigDecimal"
    you can adjust your field type in kettle according to the elastiseaech xcontent source code:
     ```java
      private void unknownValue(Object value, boolean ensureNoSelfReferences) throws IOException {
        if (value == null) {
            nullValue();
            return;
        }
        Writer writer = WRITERS.get(value.getClass());
        if (writer != null) {
            writer.write(this, value);
        } else if (value instanceof Path) {
            //Path implements Iterable<Path> and causes endless recursion and a StackOverFlow if treated as an Iterable here
            value((Path) value);
        } else if (value instanceof Map) {
            @SuppressWarnings("unchecked")
            final Map<String, ?> valueMap = (Map<String, ?>) value;
            map(valueMap, ensureNoSelfReferences);
        } else if (value instanceof Iterable) {
            value((Iterable<?>) value, ensureNoSelfReferences);
        } else if (value instanceof Object[]) {
            values((Object[]) value, ensureNoSelfReferences);
        } else if (value instanceof ToXContent) {
            value((ToXContent) value);
        } else if (value instanceof Enum<?>) {
            // Write out the Enum toString
            value(Objects.toString(value));
        } else {
            throw new IllegalArgumentException("cannot write xcontent for unknown value of type " + value.getClass());
        }
    }
    ```
