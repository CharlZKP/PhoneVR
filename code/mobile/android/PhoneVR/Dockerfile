# Use a base image that includes the required tools and environment for Android development.
# FROM gradle:8.4.0-jdk17
FROM ubuntu:22.04

# Update the package list and install essential packages
RUN apt-get update && apt-get install -y \
    software-properties-common \
    wget \
    build-essential


# RUN add-apt-repository ppa:linuxuprising/java -y
# RUN apt update && apt install oracle-java17-installer --install-recommends

RUN wget https://download.oracle.com/java/17/latest/jdk-17_linux-x64_bin.deb
RUN dpkg -i jdk-17_linux-x64_bin.deb

RUN apt install -y git curl zip jq unzip

# RUN wget -O - https://packages.adoptium.net/artifactory/api/gpg/key/public | tee /etc/apt/keyrings/adoptium.asc
# RUN echo "deb [signed-by=/etc/apt/keyrings/adoptium.asc] https://packages.adoptium.net/artifactory/deb $(awk -F= '/^VERSION_CODENAME/{print$2}' /etc/os-release) main" | tee /etc/apt/sources.list.d/adoptium.list

# RUN apt update && apt install -y \
#     temurin-21-jdk \
#     file git curl zip jq unzip

# Set up environment variables
ENV ANDROID_SDK_ROOT="/root/android-sdk-linux" \
    SDK_URL="https://dl.google.com/android/repository/commandlinetools-linux-8512546_latest.zip" \
    GRADLE_URL="https://services.gradle.org/distributions/gradle-7.5.1-bin.zip"

# Download Android SDK
RUN mkdir "$ANDROID_SDK_ROOT" .android \
 && cd "$ANDROID_SDK_ROOT" \
 && mkdir cmdline-tools \
 && cd cmdline-tools \
 && curl -o sdk.zip $SDK_URL \
 && unzip sdk.zip \
 && rm sdk.zip \
 && mv cmdline-tools latest \
 && cd .. \
 && yes | $ANDROID_SDK_ROOT/cmdline-tools/latest/bin/sdkmanager --licenses

# Install Gradle
RUN wget $GRADLE_URL -O gradle.zip \
 && unzip gradle.zip \
 && mv gradle-7.5.1 gradle \
 && rm gradle.zip \
 && mkdir .gradle

ENV PATH="/root/gradle/bin:${ANDROID_SDK_ROOT}/platform-tools:${PATH}"

RUN curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh -s -- -y
ENV PATH="${PATH}:/root/.cargo/bin"

# Copy your project files into the container
WORKDIR /phonevr
COPY . /phonevr

RUN chmod +x prepare-alvr-deps.sh && \
    bash prepare-alvr-deps.sh && \
    chmod +x ./gradlew



# Build the Android APK in the container
# RUN ./gradlew :${MAIN_PROJECT_MODULE}:assembleDebug --warning-mode=all --stacktrace -Dorg.gradle.java.home=${JAVA_HOME} && \
#     ./gradlew :${MAIN_PROJECT_MODULE}:assemble --warning-mode=all --stacktrace -Dorg.gradle.java.home=${JAVA_HOME}
    
# Set the command to run when the container starts (e.g., you might copy the APK to an output directory)
# CMD [ "cp", "${MAIN_PROJECT_MODULE}/build/outputs/apk/", "/output/android_app" ]
CMD [ "./gradlew", ":app:assemble", "--warning-mode=all", "--stacktrace", "-Dorg.gradle.java.home=${JAVA_HOME}", "&&", "cp", "app/build/outputs/apk/", "/output/android_app"]

# Optionally, you can define a volume to persist build artifacts or copy them to an external location.
VOLUME ["/output"]
