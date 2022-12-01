FROM --platform=linux/amd64 ubuntu:22.04 as builder

RUN apt-get update
RUN DEBIAN_FRONTEND=noninteractive apt-get install -y build-essential automake pkg-config libtool gtk-doc-tools libglib2.0-dev libfreetype6-dev clang pkg-config

COPY . /repo
WORKDIR /repo
RUN autoreconf -if
RUN ./configure --prefix=/install
RUN make -j8
RUN make install
RUN clang++ mayhem/fuzz_chafa_calc_canvas_geometry.cpp -fsanitize=fuzzer,address -L/install/lib -I/install/include/chafa -I/install/lib/chafa/include `pkg-config --libs --cflags glib-2.0` -lchafa -o /fuzz

FROM ubuntu:22.04 as package
RUN apt-get update
RUN DEBIAN_FRONTEND=noninteractive apt-get install -y libglib2.0-dev libfreetype6-dev
COPY --from=builder /fuzz /fuzz_chafa_calc_canvas_geometry
COPY --from=builder /install /install
ENV LD_LIBRARY_PATH=/install/lib
