FROM python:3.10.5

RUN mkdir -p /bgdatacache/
RUN mkdir -p /home/user/.config/bbglab/

ENV BGDATA_LOCAL="/bgdatacache"
ENV BBGLAB_HOME="/home/user/.config/bbglab/"

RUN apt-get update -y && apt-get upgrade -y
RUN pip install matplotlib numpy pandas
RUN pip install bgdata
RUN pip install bgreference

RUN echo "# Version of the bgdata config file\n\
version = 2\n\
\n\
# The default local folder where you want to store the data packages\n\
local_repository = \"/bgdatacache\"\n\
\n\
# The remote URL from where do you want to download the data packages\n\
remote_repository = \"https://bbglab.irbbarcelona.org/bgdata\"\n\
\n\
# If you want to force bgdata to work only locally\n\
# offline = True\n\
\n\
# Cache repositories\n\
[cache_repositories]" > /home/user/.config/bbglab/bgdatav2.conf


RUN bgdata get datasets/genomereference/hg38
RUN bgdata get datasets/genomereference/mm10
RUN bgdata get datasets/genomereference/mm39

RUN python3 -c "from bgreference import hg38, mm10, mm39; hg38(1, 1300000, 3000); mm10(1, 1300000, 3000); mm39(1, 1300000, 3000)"

RUN chmod -R 777 /bgdatacache/*
